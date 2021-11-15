---
toc : True
tags : GPU CUDA
---

``` 
ref.1: https://karl6885.github.io/cuda/2018/11/08/NVIDIA-CUDA-tutorial-1/
ref.2: https://www.sysnet.pe.kr/2/0/11481
```

# CUDA coding

* kernel: GPU Function을 부르는 용어, execution configuration에 따라 실행된다.
  * keneralFunctionName<<<number_of_blocks,threads_per_block>>>(arg1,arg2,arg..)
* thread: GPU 작업의 기본 단위. 여러 thread가 병렬적으로 작동한다.
* block: thread의 모음
* grid: 주어진 kernel의 execution configuration에서 block들의 모임, 즉 전체를 grid라 부른다.
* wrap: 32개의 thread의 다른말
##  wrap schedulaer
* 32개의 thread를 wrap 0, wrap 1,등을 다른 wrap를 수행함으로써 scheduling하면서 막는 것
* 모든 thread는 모두 동일한 kernel코드를 수행하게 된다.
* 한 block에 최대 1024개의 thread가 실행되는 것 처럼 보이지만, 사실 하나의 wrap 단위로 scheduling되면서 프로그램이 실행되고 있는 것이다. 
* Time Sharing을 하면서 Wrap가 움지이게 된다.
* 하나의 Wrap에서는 동일한 커널 코드가 실행된다. 즉, wrap는 실행단위
* 

## __global__ void GPUFunction()
* GPUFunction이라는 GPU에서 수행되는 코드가 실행된다.
* GPUFunction<<1,1>>(): 
  * main에서 해당되는 kernel함수를 다음과 같이 선언하게된다.
  * 인자 중 앞의 "1"은 실행될 쓰레드 그룹의 개수를 명시하며, block이라 부른다.
  * 인자 중 뒤의 "1"은 block내에서 몇 개의 쓰레드가 실행될 것읹3ㅣ를 명시한다.

## performWork <<<2,4>>>()
![image](https://user-images.githubusercontent.com/67637935/141710146-9597da0f-eff6-4107-9712-093da2ad749b.png)

* gridDim.x --> 2
* BlockIdx.x --> 0 or 1
* BlockDim.x --> 4
* threadIdx.x --> 0 or 1 or 2 or 3

## perforwokr2 <<<6,12>>>()
![image](https://user-images.githubusercontent.com/67637935/141717284-d1d85bfd-10e7-4631-ac0b-aa30dd8c99be.png)

* gridDim.x --> 6
* BlockIdx.x --> 0,1,2,3,4,5
* BlockDim.x --> 12
* threadIdx.x --> 0,1,2,3,4,5,6,7,8,9,10,11


# Coordinating Parallel Threads
* block에 최대 존재할 수 있는 thread의 갯수는 1024개
* block의 size는 blockDim.x로 접근할 수 있고, 인덱스는 blockIdx.x로 접근할 수 있다. 또, 각각의 thread는 threadIdx.x로 접근할 수 있다.
* 그럼으로 "threadIdx.x + blockIdx.x * blockDim.x"의 공식으로 데이터를 thread에 매필할 수 있다.

# predefined "dim3"
* dim3 DimGrid(100,500) --> 5000 thread blocks
* dim3 DimBlock(4,8,8) --> 256 threads per block
* KernelFunc <<<DimGrid,DimBlock>>>(...);

# Build 2D thread
* 기존의 kernelFunc 내부의 인자를 주는 방법으로는 1차원의 thread밖에 생성 못한다.
* predefined된 dim3 구조체를 사용하여 thread or block의 shape를 정해주면 kernel function에서 thread.x,y.z... 접근이 가능하다.

## Example
* dim3 blockshape(5,5)
*  kernelfunction <<<1,blockshape>>>(...)

![image](https://user-images.githubusercontent.com/67637935/141718056-d30324a9-dff9-4f6a-a3e7-aede2bd6b6a6.png)


## 하지만 2D 연산일지라도 실제 접근은 1차원으로 하는게 코딩하기는 펺해

* 그럼으로 다음과 같이 x,y를 통해 1차원 array로 보이는 것 처럼 바꿔줌

![image](https://user-images.githubusercontent.com/67637935/141719162-f35217b3-5453-4fd1-b9b5-4036d29b85c5.png)

* i = y + (blockDim.x)+x; //2D를 1D처럼 접근가능하도록 해줌




