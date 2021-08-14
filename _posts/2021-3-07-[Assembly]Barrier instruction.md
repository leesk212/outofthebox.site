---
tags: assembly computer-architecture
toc: True
---
barrier() 같은 경우에 다음과 같은 매크로로 define 되어 있습니다.

# define barrier() __asm__ __volatile__("": : :"memory")

보면 아무른 operation도 없는 inline assembly 코드입니다. 콜론(':')으로 구분된 "memory" 표기되어 있는 세번째 필드는 clobber 필드라고해서 해당 inline assembly가 수행된후 값이 변경되는 것들을 기입합니다.
gcc는 clobber 필드에 기입된 내용을 토대로 다음 코드를 생성하면서 최적화를 수행하는데 일반적으로 값이 변경된 것들에 대해서는 어떤 값으로 변경된지에 대한 정보가 없기 때문에 최적화를 수행할 수 없습니다.

clobber 필드에 "memory"라고 표기 하는것은 모든 메모리 타입 저장 장치(모든 레지스터, 모든 프래그, 모든 메모리 등)의 값이 변경됨을 의미합니다.
gcc는 이럴경우 __asm__ __volatile__("": : :"memory") 경계를 넘어가는 최적화 또는 instruction scheduling을 수행하지 않습니다.
때문에 __asm__ __volatile__("": : :"memory")를 사용하면 이전 코드의 수행 완료를 보장할수 있고 이후 코드가 __asm__ __volatile__("": : :"memory") 이전에 수행되는것을 방지 할수 있습니다.


<h2>배리어</h2>
<p>컴파일러나 아키텍처는 성능 향상을 위해 처리 순서에 개연성(dependency)이 없는 경우 순서를 변경하여 처리하는 최적화 동작을 한다. 그러나 개연성이 있지만 컴파일러나 아키텍처가 보기에 개연성이 없어 보이는 것으로 잘못 판단하는 경우도 있으므로 이러한 경우 미리 특정 상황에서 순서를 바꾸지 못하도록 강제해야 하는 경우가 있다. 이럴 때 명령 순서 또는 메모리 접근 동작들 사이에 순서를 바꾸지 못하게 배리어를 사용한다.</p>
<p>&nbsp;</p>
<h3>실행 오더링 (Execution Ordering)</h3>
<h5>Out-Of-Order Execution</h5>
<p>명령들이 실행될 때 성능 향상을 위해 다음과 같이 컴파일러 또는 H/W 아키텍처 내부에서의 최적화를 통해 명령 실행 순서를 바꾸는 경우들이 있다.</p>
<ul>
<li>컴파일러가 반복 실행되는 구간을 최소화 하도록 최적화
<ul>
<li>예) 1씩 100번 반복하여 증가하는 루틴 -&gt; 한 번에 100을 증가</li>
</ul>
</li>
<li>1 개의 프로세스(HW 스레드)가 명령들을 순차 처리하지 않고 일부 병렬로 처리할 때</li>
</ul>
<p>&nbsp;</p>
<p>다음은 1개의 프로세스(HW 스레드)에서 Fetch된 명령들이 동시에 처리를 시작할 수 있다.</p>
<ul>
<li>5개의 처리 유형(Integer, Multiply, Floating-Point/NENO, Branch, Load/Store)이 다른 명령이 인입되는 경우 각각에서 동시에 처리를 진행할 수 있다. 완료 시점은 명령마다 다르다. 파이프 라인 스텝(사이클)이 적은 항목이 더 빨리 처리된다. 결국 floating-point 연산 명령 후에 integer 연산이 따라오는 경우 integer 연산이 먼저 처리되어 완료되는 경우가 발생한다. 이렇게 처리 순서가 바뀔 수 있다.</li>
<li>같은 처리 유형이라도 Issue 처리기가 2개 이상일 때 동시 처리할 수 있다.</li>
</ul>
<p><a href="http://jake.dothome.co.kr/wp-content/uploads/2015/10/barriers4.png">/></a></p>
<p>&nbsp;</p>
<h3>메모리 오더링 (Memory Ordering)</h3>
<h5>Out-Of-Order Memory</h5>
<p>공유 메모리 공간을 CPU들과 디바이스들이 접근하는데 성능 향상을 위해 다음과 같이 컴파일러 또는 H/W 아키텍처 내부에서의 최적화를 통해 접근 순서를 바꾸는 경우들이 있다.</p>
<ul>
<li>컴파일러가 반복기록하는 구간을 최소화 하도록 최적화
<ul>
<li>예) A 주소에 1을 100번 반복하여 기록 -&gt; A 주소에 1을 한 번 기록</li>
</ul>
</li>
<li>아키텍처가 출력 버퍼를 사용하여 메모리 접근 순서를 바꿔 최적화
<ul>
<li>예) 캐시 라인에 속한 주소들의 액세스를 가능하면 모아서 처리</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<p>예 1) 같은 캐시 라인의 주소를 같이 처리</p>
<p>다음 그림과 같이 메모리 액세스 순서가 바뀔 수도 있다.</p>
<ul>
<li>A 주소와 B 주소가 같은 캐시라인에 있어서 가능하면 묶어 처리 (아래 그림)
<ul>
<li>A, X, B 순서대로 저장하려 했는데 A, B, X 순서로 변경됨</li>
<li>A, X, B 주소간의 연산이 없어 아키텍처는 관계(dependency)를 모르므로 순서가 바뀔 수 있다.</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<p>예 2) 디바이스 주소 레지스터와 데이터 레지스터를 사용 시 Store와 Load 순서 바뀌면서 생기는 문제</p>
<ul>
<li>디바이스 주소 레지스터 A에 주소를 보내고(write),</li>
<li>디바이스 데이터 레지스터 B에서 데이터를 받으려(read)하는데</li>
<li>아키텍처는 디바이스 레지스터 A와 B의 관계를 모른다. 이러한 경우 A와 B의 순서가 바뀔 수도 있다.</li>
</ul>
<p>&nbsp;</p>
<h3>배리어 종류</h3>
<ul>
<li>Compiler Barrier</li>
<li>Architecture Barrier</li>
<li>Implicit barriers and Etc&#8230;</li>
</ul>
<p>&nbsp;</p>
<h4>Compiler Barrier</h4>
<p>컴파일러 최적화 장벽으로 최적화 시 실행 코드가 생략, 축약 또는 실행 순서가 변경되지 못하도록 막아야하는 경우에 사용된다.</p>
<ul>
<li>C 함수
<ul>
<li>barrier()
<ul>
<li>barrier() 이전 코드와 이후 코드가 컴파일러에 의해 순서가 바뀌는 일이 없도록 제한한다.</li>
</ul>
</li>
</ul>
</li>
<li>컴파일러 지시어
<ul>
<li>__volatile__
<ul>
<li>컴파일러의 optimization을 제한하기 위해 __volatile__을 사용</li>
<li>변수 앞에 사용될 때
<ul>
<li>변수 사용에 대한 optimization을 제한한다.
<ul>
<li>컴파일러가 속도 향상을 위해 변수를 레지스터에 배치하는 것을 막는다.</li>
</ul>
</li>
</ul>
</li>
<li>함수 앞에 사용될 때
<ul>
<li>함수안의 모든 optiomization을 제한한다.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>참고: <a href="http://jake.dothome.co.kr/volatile">Volatile </a>| 문c</li>
</ul>
<p>&nbsp;</p>
<h4>Architecture Barrier</h4>
<p>가장 기본 barrier인 mb(), rmb(), wmb()를 사용하면 full 배리어로 처리 성능이 가장 느려 성능이 저하된다. 따라서 성능을 더 높이고자 여러가지 barrier를 추가하였는데 그 들간의 성능 관계는 다음 그림과 같다.</p>
<h5>Mandatory barrier</h5>
<p>아키텍처가 공유 메모리에 접근 시 생략, 축약 또는 접근 순서를 변경하지 못하도록 막는다. 다음과 같이 기본 4가지 타입의 API를 제공한다.</p>
<ul>
<li>mb()
<ul>
<li>general 메모리 배리어 타입</li>
<li>명령을 기준으로 이전 Load/Store 연산과 이후 Load/Store 연산의 순서(Ordering)를 보장한다.</li>
<li>아키텍처에 따른 구현
<ul>
<li>dsb, sync, mfence, &#8230;</li>
<li>ARM의 경우 dsb()명령이 사용되어 write 버퍼 flush이외에도 몇 가지 추가 동기화를 수행한다.</li>
<li>ARM64의 경우 dsb(sy) 명령이 사용되어 write 버퍼 flush이외에도 몇 가지 추가 동기화를 수행한다.</li>
</ul>
</li>
</ul>
</li>
<li>rmb()
<ul>
<li>read 메모리 배리어 타입</li>
<li>이 명령을 기준으로 이전 Load 연산과 이후 Load 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
<li>wmb()
<ul>
<li>write 메모리 배리어 타입</li>
<li>이 명령을 기준으로 이전 Store 연산과 이후 Store 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
<li>read_barrier_depends()
<ul>
<li>data dependency 배리어 타입</li>
<li>Load 연산으로 읽은 값을 결과로 전달해서 다음 Load 연산에 사용해야 하는 경우 atomic Load 연산간의 순서를 보장한다.</li>
<li>rmb()보다 조금 더 빠른 성능이다.</li>
<li>ARM, ARM64의 경우 아키텍처 내에 간단한 dependency를 체크하므로 별도로 실행할 코드가 없다.</li>
<li>오직 alpha 아키텍처에 관련 코드가 구현되어 있다.</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<p>추가로 다음 2가지의 묵시적인 one-way 배리어 타입이 있다. (자세한 설명은 좀 뒤에..)</p>
<ul>
<li>ACQUIRE operations
<ul>
<li>one-way 배리어로 ACQUIRE 조작 이후의 메모리 조작 오더를 보장한다.</li>
<li>LOCK operations와 smp_load_acquire() 및 smp_cond_acquire() 에서 사용된다.</li>
<li>smp_load_acquire()
<ul>
<li>이 명령을 기준으로 <span style="color: #ff6600;">이후</span> Read/Write 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
</ul>
</li>
<li>RELEASE operations
<ul>
<li>one-way 배리어로 RELEASE 조작 이전의 메모리 조작 오더를 보장한다.</li>
<li>UNLOCK operations와 smp_store_release() 에서 사용된다.</li>
<li>smp_store_release()
<ul>
<li>이 명령을 기준으로 <span style="color: #0000ff;">이전</span> Read/Write 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<p>다음 그림은 ACQUIRE 및 RELEASE operation이 spinlock에 구현된 사례를 보여준다.</p>
<ul>
<li>A 값이 RELEASE 뒤로 순서가 바뀌지 않게 막으므로 안전하게 B에 A 값이 저장됨을 알 수 있다.</li>
</ul>
<h5>device 지원 barrier</h5>
<p>디바이스를 위해 rmb(), wmb()보다 더 가볍고 smp 지원 barrier보다는 좀 더 무거운 outer share 영역까지 공유 가능한 명령을 커널 v3.19-rc1에 추가하였다.</p>
<ul>
<li>참고: <a href="https://github.com/torvalds/linux/commit/1077fa36f23e259858caf6f269a47393a5aff523#diff-c3f5e88f504e43a4ce9e1b76792fd1c6">arch: Add lightweight memory barriers dma_rmb() and dma_wmb()</a></li>
</ul>
<p>&nbsp;</p>
<ul>
<li>dma_rmb()
<ul>
<li>rmb()와 동일하나 cpu 뿐만 아니라 outer 영역의 디바이스도 접근하는 공유 메모리이다.</li>
</ul>
</li>
<li>dma_wmb()
<ul>
<li>wmb()와 동일하나 cpu 뿐만 아니라 디바이스도 접근하는 공유 메모리이다.</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h5>smp 지원 barrier</h5>
<p>SMP 시스템의 inner share 영역에서 캐시 일관성을 같이 사용하는 코어 및 디바이스들간 메모리 정합성을 보장하기 위해 다음 함수들을 사용한다.</p>
<ul>
<li>smp_mb()
<ul>
<li>이 명령을 기준으로 이전 Read/Write 연산과 이후 Read/Write 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
<li>smp_rmb()
<ul>
<li>이 명령을 기준으로 이전 Read 연산과 이후 Read 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
<li>smp_wmb()
<ul>
<li>이 명령을 기준으로 이전 Write 연산과 이후 Write 연산의 순서(Ordering)를 보장한다.</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h4>Implicit barriers and Etc&#8230;</h4>
<p>커널내의 Locking 구조, pthread 동기화 연산들도 implicit 배리어로 동작한다. 하지만 이 자원들을 외부로 공유시에는 명시적인 배리어가 필요할 수 있다.</p>
<p>&nbsp;</p>
<h2>ACQUIRE/RELEASE 구현</h2>
<p>메모리 배리어 요구 시 대부분의 아키텍처는 Read/Write에 관여하는 버퍼가 모두 다 처리되어 비워질(flush) 때까지 기다리는 것으로 처리한다. 그러나 이러한 처리에 상당한 사이클을 사용하므로 성능을 떨어뜨리는 이유가 된다. 보다 빠른 처리를 위해 TSO(Total Store Order)가 지원되는 x86 및 sparc 아키텍처와 Load-Acquire/Store-Release 명령(ldar/stlr)이 지원되는 ARM64 아키텍처를 위해 커널 v3.14-rc1에서 특별한 구현이 소개되었다.</p>
<ul>
<li>참고: <a href="https://github.com/torvalds/linux/commit/47933ad41a86a4a9b50bed7c9b9bd2ba242aac63#diff-c3f5e88f504e43a4ce9e1b76792fd1c6">arch: Introduce smp_load_acquire(), smp_store_release()</a></li>
<li>지원되지 않는 아키텍처들은 smp_mb()로 구현된다.</li>
</ul>
<p>&nbsp;</p>
<h5>TSO(Total Store Order) 지원 아키텍처</h5>
<p>non-TSO 아키텍처에서는 load/store 연산들이 순차적으로 진행되어 순서가 바뀌는 일이 없다. 그러나 성능 향상을 위해 TSO를 지원하는 아키텍처의 경우 Store만 순서대로 처리하고, Load는 weakly(out-of) order로 처리 할 수 있다.</p>
<ul>
<li>store 요청 시 아직 처리되지 않아 write buffer에서 대기하는 store할 값을 뒤 따르는 load가 이를 읽을 수 있어 이러한 경우 load가 먼저 처리될 수 있다.</li>
<li>이러한 TSO 지원 아키텍처에서는 순서가 바뀌어도 문제가 없으므로 store 및 release operation 구현 시 컴파일러 배리어만 포함하면된다.</li>
</ul>
<p>&nbsp;</p>
<p>다음 그림은 non-TSO 와 TSO 지원 아키텍처를 비교한 그림이다.</p>
<ul>
<li>TSO 아키텍처에서 write buffer에 있는 A값을 메모리에 기록하지 않아도 먼저 load를 할 수 있다.</li>
</ul>
<h5>Acquire/Store-Release 명령(ldar/stlr) 지원 아키텍처</h5>
<p>다음 그림은 ARMv8 아키텍처에서의 양방향 barrier와 단방향 barrier의 차이를 보여준다.</p>
<ul>
<li>lock()과 unlock()을 구현 시 2개의 양방향 barrier를 사용하는 것 보다 단방향 barrier 페어(pair)를 사용하여 성능을 높일 수 있다.</li>
<li>ARMv7은 이러한 명령을 지원하지 않아 기존 smp_mb() 명령을 사용하여 구현한다.</li>
<li>ARMv8에서 이러한 단방향(one-way) barrior 명령 2개를 하나의 페어(pair)로 구성한다.
<ul>
<li>단방향 배리어인 ldar로 smp_load_acquire()</li>
<li>단방향 배리어인 stlr로 smp_store_release()</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h2>Barriers of ARMv7 &amp; ARMv8</h2>
<ul>
<li>DMB, DSB, ISB의 세 명령</li>
<li>ARMv6까지는 CP15 레지스터를 이용하여 명령을 수행하지만 ARMv7부터는 전용 명령을 사용한다.</li>
</ul>
<p>&nbsp;</p>
<h3>DMB(Data Memory Barrier)</h3>
<ul>
<li>데이터 메모리 배리어로 DMB 명령 전과 명령 후의 메모리 접근을 분리하게 만드는데 다음 두 가지 인수를 결합하여 사용할 수 있다.
<ul>
<li>shareablity domain
<ul>
<li>ish: inner-shareable</li>
<li>osh: outer-shareable</li>
<li>sy: system-shareable</li>
</ul>
</li>
<li>access types
<ul>
<li>ld: load</li>
<li>st: store</li>
</ul>
</li>
</ul>
</li>
<li>모든 지연된 load/store가 모두 완료(flush)될 때까지 대기한다.
<ul>
<li>예) R,W,R,W,R,W → 효율을 위해 R,R,R,W,W,W 순으로 바꿀 수 있는데 이를 방지하기 위함.</li>
</ul>
</li>
</ul>
2015/10/barriers2.png 405w, http://jake.dothome.co.kr/wp-content/uploads/2015/10/barriers2-300x116.png 300w" sizes="(max-width: 405px) 100vw, 405px" /></a></p>
<p>&nbsp;</p>
<h3>DSB(Data Synchronization Barrier, called DWB)</h3>
<ul>
<li>아래의 항목들(DMB 명령이 하는일 포함)이 완료될 때까지 추가 명령이 실행되는 것을 멈추고 기다리므로 DMB보다 훨씬 느리다. 인수의 사용은 DMB와 동일하다.
<ul>
<li><span style="color: #000000;">Instruction 캐시 및 Data 캐시 조작</span></li>
<li><span style="color: #000000;">Branch predictor 캐시 flush</span></li>
<li><span style="color: #000000;">지연된 load/store 명령의 처리 &lt;- <span style="color: #ff6600;">DMB 명령이 하는 일</span></span></li>
<li><span style="color: #000000;">TLB 캐시 조작 완료</span></li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h3>ISB(Instruction Synchronization Barrier)</h3>
<ul>
<li>ISB 명령이 동작하는 순간 파이프라인으로 인해 다음 명령이 뒤 따라 들어오게 되는데 이를 모두 버리게한다.(파이프라인 Flush) Out of order execution 기능으로 인해 뒤 따라 Fetch된 명령이 먼저 동작이 될 가능성이 있는데 이럴 경우 문제가 벌어질 가능성이 있다. 특별히 두 명령의 우선 순위를 확실히 구분해야 하는 루틴에서는 두 명령 사이에 ISB를 실행시켜 두 명령어의 실행 순서를 명확히 보장한다.</li>
<li>ARMv7에서는 ISB를 지원하지만 다른 아키텍처에서 ISB를 지원하지 않는 경우가 있다. 이럴때 파이프 라인을 비우는 것과 비슷한 효과를 내려면 nop 또는 mov a0, a0등의 명령 사용을 사용하여 명령어 수행이 바뀌는 순간에도 문제가 없도록 할 수 있다. 또한 메모리 참조의 순서를 dependency하게 유도하여 일정 루틴을 in-order로 수행될 수 밖에 없도록 만들기도 한다.</li>
<li>사용 Case
<ul>
<li>실시간 코드 변경
<ul>
<li> 만일 코드부분이 바뀐 후 캐시된 명령이 재실행되면 문제가 발생되므로 이 때에도 ISB를 사용하여야 한다. (JIT가 명령을 바꾸면서 동작)</li>
</ul>
</li>
<li>MMU on/off
<ul>
<li>MMU 전환 시점에도 ISB 명령을 사용함.</li>
<li>Out of Order Execution: CPU가 Out of Order Execution(ARMv6 아키텍처 부터 지원하지만 대부분 ARMv7부터 제품이 있음)을 지원하는 병렬 Pipeline을 사용하는 경우 캐시 사용이 바뀌는 시점에 이전 명령이 다음 이어지는 명령과 연관성이 없는 경우 다음 명령이 먼저 실행되면서 MMU 상태가 바뀌기 전후로 주소 참조에 문제가 될 수 있음을 막기 위함이다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h2>Barrier Options</h2>
<p>&nbsp;</p>
<h2>배리어 사용처</h2>
<ul>
<li>SIMPLE ORDERING AND BARRIER CASES
<ul>
<li>Simple Weakly Consistent Ordering Example</li>
<li>Weakly-Ordered Message Passing problem</li>
<li>Address Dependency with object construction</li>
<li>Causal consistency issues with Multiple observers</li>
<li>Multiple observers of writes to multiple locations</li>
<li>Posting a Store before polling for acknowledgement</li>
<li>WFE and WFI and Barriers</li>
</ul>
</li>
<li>LOAD EXCLUSIVE/STORE EXCLUSIVE AND BARRIERS
<ul>
<li>Acquiring a Lock</li>
<li>Releasing a Lock</li>
<li>Use of Wait For Event (WFE) and Send Event (SEV) with</li>
</ul>
</li>
<li>SENDING INTERRUPTS AND BARRIERS
<ul>
<li>Using a Mailbox to send an interrupt</li>
</ul>
</li>
<li>CACHE &amp; TLB MAINTENANCE OPERATIONS AND BARRIERS
<ul>
<li>Data Cache maintenance operations</li>
<li>Instruction Cache Maintenance operations</li>
<li>TLB Maintenance operations and Barriers</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>
<h2>코드 분석</h2>
<h5>UP 시스템</h5>
<p>arch/arm/include/asm/barrier.h</p>
<pre class="brush:c">#define mb()        barrier()
#define rmb()       barrier()
#define wmb()       barrier()

#define dma_rmb()   barrier()
#define dma_wmb()   barrier()</pre>
<p>CPU가 하나만 사용된 시스템에서는 메모리 접근 순서가 바뀌더라도 특별히 영향을 주지 않으므로 컴파일러 배리어만 사용한다.</p>
<ul>
<li>UP 시스템이더라도 Write 버퍼가 적용된 시스템의 경우 디바이스와의 연동에서 영향을 받기 떄문에 아래 SMP 코드를 수행한다.</li>
</ul>
<p>&nbsp;</p>
<h5>ARMv7 SMP</h5>
<p>include/asm-generic/barrier.h</p>
<pre class="brush:c">#define smp_mb()  __smp_mb()
#define smp_rmb() __smp_rmb()
#define smp_wmb() __smp_wmb()</pre>
<p>&nbsp;</p>
<p>arch/arm/include/asm/barrier.h</p>
<pre class="brush:c">#define isb(option) __asm__ __volatile__ ("isb " #option : : : "memory")
#define dsb(option) __asm__ __volatile__ ("dsb " #option : : : "memory")
#define dmb(option) __asm__ __volatile__ ("dmb " #option : : : "memory")

#define __arm_heavy_mb(x...) dsb(x)

#define mb()            __arm_heavy_mb()
#define rmb()           dsb()
#define wmb()           __arm_heavy_mb(st)

#define dma_rmb()       dmb(osh)
#define dma_wmb()       dmb(oshst)

#define __smp_mb()      dmb(ish)
#define __smp_rmb()     __smp_mb()
#define __smp_wmb()     dmb(ishst)
</pre>
<ul>
<li>외부 캐시와 연동된 특정 시스템의 경우 dsb() 호출 이외에도 outer sync 관련 custom 함수를 호출한다.</li>
<li>ARMv6의 경우 내장된 isb, dsb, dmb 명령과 동일한 역할을 수행하는 coprocess 레지스터(mcr p15)를 사용한다.</li>
</ul>
<p>&nbsp;</p>
<h5>ARMv8</h5>
<p>include/asm-generic/barrier.h</p>
<pre class="brush:c">#define smp_mb()  __smp_mb()
#define smp_rmb() __smp_rmb()
#define smp_wmb() __smp_wmb()</pre>
<p>&nbsp;</p>
<p>arch/arm64/include/asm/barrier.h</p>
<pre class="brush:c">#define mb()            dsb(sy)
#define rmb()           dsb(ld)
#define wmb()           dsb(st)

#define dma_rmb()       dmb(oshld)
#define dma_wmb()       dmb(oshst)

#define __smp_mb()      dmb(ish)
#define __smp_rmb()     dmb(ishld)
#define __smp_wmb()     dmb(ishst)</pre>
<p>&nbsp;</p>

<h2>참고</h2>
<ul>
<li><a href="http://jake.dothome.co.kr/volatile">Volatile </a>| 문c</li>
</ul>
<p>&nbsp;</p>
<ul>
<li><a href="https://lwn.net/Articles/576486/">Memory barriers for TSO architectures</a> | LWN.net</li>
<li>ARM Barrier Litmus Tests and Cookbook &#8211; <a href="http://infocenter.arm.com/help/topic/com.arm.doc.genc007826/Barrier_Litmus_Tests_and_Cookbook_A08.pdf">다운로드</a></li>
<li>Memory Barriers: a Hardware View for Software Hackers &#8211; <a href="http://www.rdrop.com/users/paulmck/scalability/paper/whymb.2010.07.23a.pdf">다운로드</a></li>
<li><a href="http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.faqs/ka14041.html">In what situations might I need to insert memory barrier instructions?</a> | ARM</li>
<li><a href="http://www.iamroot.org/xe/FreeBoard/234956">DMB &amp; ILP</a> | iamroot 11차-A팀</li>
<li><a href="https://www.kernel.org/doc/Documentation/memory-barriers.txt">Documentation/memory-barriers.txt</a> | Kernel.org</li>
<li><a href="https://lkml.org/lkml/2016/3/9/962">Documentation/memory-barriers.txt의 한글 번역</a> | 박성재</li>
<li>Memory Barriers in the Linux Kernel (2016) | SUSE &#8211; <a href="http://events17.linuxfoundation.org/sites/events/files/slides/dbueso-elc2016-membarriers-final.pdf">다운로드 pdf</a></li>
</ul>
* reference: http://jake.dothome.co.kr/barriers/
