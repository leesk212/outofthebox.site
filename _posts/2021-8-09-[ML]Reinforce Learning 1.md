---
tags: ML idec Reinforce_Learning
toc: True
---
# 강화학습이 다루고 있는 문제
> y(t+1) = f(y(t),x(t))
* 상대방이 입력을 학습을 같이한다.

# Development flow
* Optimal control
* Dynamic programming
* Principle of optimality
* Reinforcement learning

# Solution: error backprop through time
내가 졌다! 에 대한 상태를 더 강화시켜야함. 뭔가 리워드가 주어졌을 때, 실수했으면 과거의 정보를 다시 바로 잡아야함.
이겨도 이긴 신호를 더 잘 이기는 방법으로 수정해야함.
## supervised learning 에서 backpropagation과 다른점
같은 공간에 대한 전파이다. 하지만 reinforce learning은 이전 공간(시점)까지 가서 에러를 잡아야한다. 
그렇기에 이론적으로 더 많이 접근과 시간이 필요하였다.

# Dynamic programing
* 
