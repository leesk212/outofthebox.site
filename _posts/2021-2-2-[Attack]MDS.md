요약
네 가지 새로운 마이크로프로세서 취약점이 발견되었으며 Red Hat 제품 보안팀은 이러한 취약점을 심각한 보안 문제를 일으키는 "중요" 등급으로 분류하고 있습니다. 시스템에 대한 로컬 쉘 액세스 권한이 있는 공격자가 이러한 보안 취약점을 악용할 경우 CPU 캐시의 데이터를 승인되지 않은 프로세스에 노출할 수 있습니다. 이러한 취약점을 악용하여 시스템에 보안 공격을 구현하는 것이 어렵기는 하지만 숙련된 공격자는 이를 악용하여 가상 또는 컨테이너 인스턴스나 기본 호스트 시스템에서 메모리 데이터를 읽을 수 있습니다. Red Hat은 영향을 받은 시스템에 대응할 수 있는 완화 방법을 마련하고 사용자에게 이러한 보안 취약점을 평가하고 대처하기 위해 필요한 세부 단계를 제공합니다.

문제 세부 정보 및 배경 정보
Red Hat은 권한이 없는 로컬 공격자가 일반 메모리 보안 제한을 우회하여 권한이 필요한 메모리 데이터에 액세스할 수 있게 하는 일련의 마이크로 아키텍처 (하드웨어) 구현 문제를 인지하고 있습니다. 이러한 결함은 컨테이너에서 실행되는 악의적인 코드에 의해서도 악용될 수 있습니다. 이러한 문제는 최신 Intel 마이크로 프로세서에 영향을 미치며 사용자는 Linux 커널, 가상화 스택, CPU 마이크로코드를 업데이트해야합니다. 이러한 문제점은 CVE-2018-12130로 지정되어 있으며 중요(Important) 보안 등급으로 분류되어 있습니다. CVE-2018-12126, CVE-2018-12127, CVE-2019-11091 로 지정된 문제는 중간 (Moderate) 보안 등급으로 분류되어 있습니다.

현재 이러한 취약점은 Intel 기반 프로세서에만 영향을 미치는 것으로 알려져 있지만 Red Hat 제품 보안팀에서는 SMT (Simultaneous Multi Threading) 관련 보안 취약점이 다른 공급 업체의 제품에 미치는 영향을 계속 조사하고 있습니다.

Intel 마이크로프로세서 설계가 여러 성능 미세 최적화를 구현하는 방식에서 결함이 발견되었습니다. 취약점이 악용될 경우 공격자는 사이드 채널 공격을 사용하여 다른 프로세서, 컨테이너, 가상 시스템, 커널에 속한 시스템에서 최근 사용된 데이터에 액세스할 수 있습니다.

이러한 보안 취약점은 내부 CPU 구조에 남아있는 데이터를 얻기 위해 마이크로 프로세서의 추측 실행을 활용하기 때문에 마이크로아키텍처 데이터 샘플링 (MDS: Microarchitectural Data Sampling)이라고 합니다.

CVE-2018-12126 - 마이크로아키텍처 스토어 버퍼 데이터 샘플링 (MSBDS: Microarchitectural Store Buffer Data Sampling)

최근 메모리에 저장 (쓰기)이 포함된 프로세서 스토어 버퍼 구조에 정보 유출 가능성과 관련된 많은 Intel 마이크로프로세서 설계에서 취약점이 발견되었습니다.

최신 Intel 마이크로프로세서는 하드웨어 수준의 마이크로 최적화를 구현하여 CPU 캐시 작업에 대한 데이터 쓰기 성능을 개선했습니다. 쓰기 동작은 STA (STore Address) 및 STD (STore Data) 하위 작업으로 분할되어 있습니다. 이러한 하위 작업을 통해 프로세서는 주소 생성 논리를 하위 작업으로 전달하여 쓰기 작업의 성능을 최적화할 수 있습니다. 두 하위 작업 모두 '프로세서 스토어 버퍼'라는 공유 분산 프로세서 구조에 데이터를 기록합니다.

프로세서 스토어 버퍼는 주소, 값, '유효' 항목으로된 테이블입니다. 하위 작업은 서로 독립적으로 실행할 수 있으므로 테이블의 주소 및 값 항목에 있는 데이터를 각각 독립적으로 업데이트할 수 있습니다. 이는 다른 시점에서는 주소 또는 값이 유효하지 않을 수 있음을 의미합니다.


프로세서는 스토어 버퍼로부터 엔트리를 추측하여 전송할 수 있습니다. 위의 분할 설계를 사용하면 이러한 잘못 전송된 주소와 같은 이전 값과 관련이 없는 저장소에서 반환된 데이터와 같은 확인되지 않은 데이터의 사용을 추측할 수 있습니다. 이러한 오류는 오류/지원 해결에 따라 다시 실행되는 로드에만 발생하므로 프로그램의 아키텍처에 영향을 주지는 않지만 사이드 채널 분석을 통해 데이터를 검색하기 위한 악성 코드에 스토어 버퍼 상태가 유출될 수 있습니다.

프로세서 스토어 버퍼 항목은 활성 하이퍼 스레드 수 간에 동일하게 분배됩니다. 전원 상태 변경과 같은 조건은 항목이 제거되었는지 확인하지 않고 프로세서 스토어 버퍼 항목을 부분적으로 업데이트된 상태의 프로세서로 다른 스레드에 재할당할 수 있습니다.

이러한 문제는 Fallout이라고 합니다.


CVE-2018-12127 - 마이크로아키텍처 로드 포트 데이터 샘플링 (MLPDS: Microarchitectural Load Port Data Sampling)

마이크로프로세서는 '로드 포트'를 사용하여 메모리 또는 IO에서 로드 연산을 수행합니다. 로드 작업 중 로드 포트는 메모리 또는 IO 서브시스템에서 데이터를 수신한 후 데이터를 CPU 파이프 라인에 있는 CPU 레지스터 및 처리 대기열 작업에 제공합니다.

일부 구현에서 각 로드 포트 내의 라이트백(writeback) 데이터 버스는 새로운 로드 연산이 해당 데이터를 덮어쓰기할 때 까지 이전 로드 연산으로 부터의 데이터 값을 저장할 수 있습니다.

MLPDS는 다음과 같은 경우 악의적인 사용자에게 기존 로드 포트 데이터를 제공할 수 있습니다:

오류 발생/보조 SSE/AVX/AVX-512 로드가 64 비트 크기 이상으로 로드된 경우
오류 발생/보조 로드가 64 바이트의 경계를 초과한 경우
위의 경우 로드 오퍼레이션은 내부 데이터 구조에서 부적절한 데이터 값을 추측하여 종속 오퍼레이션에 제공합니다. 이러한 데이터를 추측하여 전달하면 프로그램 실행이 변경되지는 않지만 이를 위젯으로 사용하면 로드 포트에 액세스하는 시점에 따라 공격자가 다른 프로세스의 데이터 값을 추측할 수 있습니다.


CVE-2018-12130 - 마이크로아키텍처 필 버퍼 데이터 샘플링 (MFBDS: Microarchitectural Fill Buffer Data Sampling)

이는 Red Hat에서 '중요'한 보안 영향을 미치는 것으로 분류된 위험이 높은 취약점입니다. 이는 Intel 마이크로프로세서에 의해 사용되는 필 버퍼 (fill buffers) 구현에서 발견되었습니다.

필 버퍼는 존재하지 않는 데이터 값을 사용 시도하고 그 결과 프로세서 L1 데이터 캐시에서 누락된 데이터를 저장하게 됩니다. Intel 코어에서 L1 데이터 캐시 누락이 발생하면 필 버퍼는 프로세서가 상위 레벨 캐시의 액세스 데이터를 로드하는 동안 다른 작업을 계속 처리할 수 있도록 설계되었습니다. 또한 L1 데이터 캐시에 데이터를 기록하지 않고 실행 유닛(Execution Unit)으로 직접 데이터를 전달할 수 있습니다.

로드 오퍼레이션의 분리 조작은 스토어에서와 같이 분리되지 않지만 주소 생성 장치 (AGU: Address Generation Unit) 작업이 필요합니다. AGU가 오류 (#PF 등) 또는 보조 (A/D 비트)를 생성하면 기존 Intel 설계로 인해 로드 작업의 실행이 차단되고 나중에 로드 작업을 다시 실행합니다. 현재의 설계에서는 로드가 실제로 실행되기 전에 후속 추측 작업이 필 버퍼 슬롯에서 전달된 데이터에 일시적으로 액세스할 수 있습니다. 따라서 필 버퍼 항목을 덮어쓰지 않으면 다른 스레드가 최근에 액세스한 데이터를 읽을 수 있습니다.

이러한 문제는 RIDL 또는 ZombieLoad라고 합니다.

CVE-2019-11091 - 마이크로아키텍처 데이터 샘플링에서 캐시 불가능한 메모리 (MDSUM: Microarchitectural Data Sampling Uncacheable Memory)

L1 CPU 캐시에서 캐시-누락이 발생했을 때 최신 CPU가 사용하는 메커니즘인 '필 버퍼' 구현에서 취약점이 발견되었습니다. 공격자가 페이지 오류를 발생시키는 로드 작업을 생성할 때 상위 레벨 캐시에서 데이터를 가져오는 동안 프로세서는 잘못된 필 버퍼의 데이터를 사용하여 실행을 예측합니다. 이러한 응답 시간를 측정하여 필 버퍼에서 데이터를 추측할 수 있습니다.

감사 인사
Red Hat은 이 문제에 대해 보고하고 완화 조치에 대해 협력해 주신 Intel 및 업계 파트너에게 감사드립니다. 

또한 처음으로 문제에 대해 보고해 주신 분들에게도 감사드립니다.
Microarchitectural Store Buffer Data Sampling (MSBDS) - CVE-2018-12126
이 취약점은 Intel 직원에 의해 발견되었습니다. Intel은 Ke Sun, Henrique Kawakami, Kekai Hu, Rodrigo Branco님에게 감사드립니다. 개별적으로 문제에 대해 보고해 주신 Lei Shi - Qihoo - 360 CERT 및 Marina Minkin, Daniel Moghimi, Moritz Lipp, Michael Schwarz, Jo Van Bulck, Daniel Genkin, Daniel Gruss, Berk Sunar, Frank Piessens님에게 감사드리며, Yuval Yarom (1University of Michigan, Worcester Polytechnic Institute, Graz University of Technology, imec-DistriNet, KU Leuven, University of Adelaide)님에게도 감사드립니다. 

Microarchitectural Load Port Data Sampling (MLPDS) - CVE-2018-12127
이 취약점은 Intel 직원 및 Microsoft에 의해 발견되었습니다. Intel은 Brandon Falk – Microsoft Windows Platform Security Team, Ke Sun, Henrique Kawakami, Kekai Hu, Rodrigo Branco - Intel 님에게 감사드립니다. 문제에 대해 개별적으로 보고해 주신 Matt Miller – Microsoft, Stephan van Schaik, Alyssa Milburn, Sebastian Österlund, Pietro Frigo, Kaveh Razavi, Herbert Bos, Cristiano Giuffrida - VUSec group at VU Amsterdam 님에게도 감사드립니다.

Microarchitectural Fill Buffer Data Sampling (MFBDS) - CVE-2018-12130

이 취약점은 Intel 직원에 의해 발견되었습니다. Intel은 Ke Sun, Henrique Kawakami, Kekai Hu, Rodrigo Branco님에게 감사드립니다. 개별적으로 보고해 주신 Giorgi Maisuradze – Microsoft Research, Dan Horea Lutas 및 Andrei Lutas - Bitdefender, Volodymyr Pikhur, Stephan van Schaik, Alyssa Milburn, Sebastian Österlund, Pietro Frigo, Kaveh Razavi, Herbert Bos, Cristiano Giuffrida - VUSec group at VU Amsterdam, Moritz Lipp, Michael Schwarz, Daniel Gruss - Graz University of Technology 님에게도 감사드립니다.

Microarchitectural Data Sampling Uncacheable Memory (MDSUM) - CVE-2019-11091

이 취약점은 Intel 직원이 내부적으로 발견했습니다. Intel은 Ke Sun, Henrique Kawakami, Kekai Hu, Rodrigo Branco님에게 감사드립니다. 문제에 대해 개별적으로 보고해 주신 Volodrmyr Pikhur, Moritz Lipp, Michael Schwarz, Daniel Gruss - Graz University of Technology, Stephan van Schaik, Alyssa Milburn, Sebastian Österlund, Pietro Frigo, Kaveh Razavi, Herbert Bos, Cristiano Giuffrida - VUSec group at VU Amsterdam님에게도 감사드립니다.

