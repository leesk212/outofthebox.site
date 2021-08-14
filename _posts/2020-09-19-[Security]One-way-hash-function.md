---
tags: security-defense system-security crpyto
toc: True
---
# Hash-Function
* cryptography과정에서 필수적인 요소이다.
* One-way이여야 하며, 같은 Hash값을 갖는 경우를 제거해야만 한다.
* 무결성보증, PW 인증, Block Chain등에서 쓰인다.
* 가능한 공격으로는 Length extension attack과 Collision attack이 있다.

## 무한집합에서 유한집합을 만드는 과정이다.
* f(x) = x mod 10000 이다.
* hash(m1) = h1, hash(m2) = h2라는 가정하에 hash^-1(h1)이 쉽게 파악되어서는 안되고, h1와 h2가 같은 것을 쉽게 만들 수 있으면 안된다.
* 종류로는 MD와 SHA가 있다.

## MD 
* Message Digest(축약)의 약어이다.
* 임의의 길이 M이 Hash function이 될 경우, 16byte의 hash가 된다.
* 2004년 MD5가 쉽게 풀리는 algorithm이 개발 되었다.

## SHA
* Standard Hash Algorithm의 약어이다.
* NIST로부터 표준화 되었으며, SHA-2가 가장많이 쓰인다.
* SHA-256의 뜻은 block단위로 bit를 나누는데, 그 길이가 256byte라는 것이다.

## One-way Hash Algorithm
* One-way hash를 만드는 법은 다음과 같다.
initial vector의 값과 , M1(Block 단위로 Message를 나눈값)을 hash 알고리즘에 넣고, 이것을 4번 반복한 후 마지막은 Padding한 값과 함께 hash 알고리즘에 넣어 MD5를 만든다.
* Make hash in linux
```
$md5sum file.c
$sha256sum file.c
$echo -n "hello Work" | sha256sum
```
* Make hash by openssl (dgst => digest)
```
#sha256 algoritms으로 hashing한다.
$openssl dgst -sha256 file.c
$openssl sha256 file.c
#md5 algoritms으로 hashing한다.
$openssl md5 file.c
$openssl dgst -md5 file.c
```

## Advantage of Hash
1. bidding: 모두가 다 같이 투자한 금액을 한번에 파악할 수 없으니 공정한 bidding이 된다.
2. Password Verification: linux의 /etc/shadow file을 확인해보면 hashing된 password를 볼 수 있다.
> seed:$6(Hashig algoritm type)$(salt: 난수값)$(Hash)


## Salt
* 난수값과 함께 hashing이 되면 같은 input이라고 해도, hashing의 결과값이 달라지기에 유용하다.
* one-way hash rounds(password||random string)
```
$python
> import cryto
> print crypto.crypt('dees', '$6$wRdsaewaf$') #random salt값 연결
...
```
* Dictionary attack/Ranbow attack 예방가능
> 공격 후 동일한 hash값을 갖고 있어봤자 소용이 없음  
> 계속 결과된 hash 값이 바뀌기 때문에 매번 새로운 공격을 해서 hash값을 알아내야함

## hash의 사용
1. TSA(Timestamping)
2. MAC(Message Authentication Code)
> 중간자 공격을 막을 수 있음  
> 중간자공격: A->Z->B Z가 A의 output을 가로채서 B에게 악성 값을 주는 과정  
하지만 Hash값과 비밀키가 있으면 B에게 들어오는 input이 정확한 값인지 아닌지 확인할 수 있음

## hash 파악 공격
1. collision Attacks 
prefix를 추가한 후 md5collgen 알고리즘을 사용하면 손쉽게 같은 값의 hash를 만들 수 있다.
```
md5collgen -p prefix.txt -o out1.bin out2.bin
```
2. length extension
hash값이 같았던 파일은 suffix를 똑같이 더해버리면 hash값이 같아져 버린다.
