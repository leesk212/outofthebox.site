---
toc : true
---

# map
* 여러 개의 데이터를 한 번에 다른 형태의 데이터로 변환하기 위해서 사용
> map(변환 함수, 순환 가능한 데이터)   
## Example 
### 1: When getting input data
```python 
n,m = map(int, input().split())
```
* input() api로 입력 값을 받게 되면 list의 string의 형태로 받아지기에 이것을 split() method를 사용하면 split안의 인자에 따라서 나눠지고 list로 반환이 된다. 
* 그 list로 반환된 값을 int로 mapping 시키는 과정

