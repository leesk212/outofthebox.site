---
tags: machine-learning idec reinforce_learning
toc: True
---
# 강화학습이 다루고 있는 문제
> y(t+1) = f(y(t),x(t))
* 상대방이 입력을 학습을 같이한다.

# Solution: error backprop through time
내가 졌다! 에 대한 상태를 더 강화시켜야함. 뭔가 리워드가 주어졌을 때, 실수했으면 과거의 정보를 다시 바로 잡아야함.
이겨도 이긴 신호를 더 잘 이기는 방법으로 수정해야함.
## supervised learning 에서 backpropagation과 다른점
같은 공간에 대한 전파이다. 하지만 reinforce learning은 이전 공간(시점)까지 가서 에러를 잡아야한다. 
그렇기에 이론적으로 더 많이 접근과 시간이 필요하였다.

## Dynamic programing
* 전체의 문제를 하나의 문제로 보지않고 작은 문제들로 쪼개서(scale이 작도록) 계속적으로 scale을 늘려나가면서 전체 문제를 푸는 것.
* 이것을 하기 위해서는 memory를 써야함 (이전에 사용하였던 challenge를 다시 가져오기 위해서)
* 강화학습은 이 원리에서 출발함
  *  작은 스케일에서 풀리면 조금 더 큰문제 풀고, 다시 조금 더 큰 문제를 풀고 (메모리를 써서 풀기)

## Temporal Credit assignment problem
* reward를 주면서 이기는 것을 더 많이 이길 수 있도록 진행
* 어떻게 풀거냐? --> DP


# Development flow
## Optimal control
## Dynamic programming
* ![image](https://user-images.githubusercontent.com/67637935/128654656-0c27cda9-6b61-4093-956d-80f3c4891b53.png)

## Principle of optimality
  * 조그마한 문제부터 풀어나가면서 전체를 적용시킬 수 있음 (subproblem set --> max)
  * subset이 전체 set으로 포괄될 수 있도록 같은 propertiy를 갖고 있어야함
  * e.g) 이동거리 최단거리 찾기
  > ![image](https://user-images.githubusercontent.com/67637935/128654237-2368efab-a96e-4065-9879-026b06e60500.png)
## Reinforcement learning
  * backward 이후에 다시 forward 진행
  * forward진행중에 계산해본 것 중에서 가장 minimun하는 것을 update시키는 것
  * forward가 진행하기 위해서는 미리 계산을 위한 J가 있어야함 (J: subset 전략)


# Terminology
* value function --> 내가 현재 하는 가치가 얼마나 차이가 있는 상황인지 (현재 상황 s에서 action을 계속적으로 진행했을 때 reward)

