---
tags: security-defense crpyto
toc: True
---
# Introduction

암호학은 2가지 분야가 있다. 
* 암호 설계: [방패]암호들을 설계 하며 암호를 연구하고 더 고도화된 알고리즘을 통해 하며 **방패**를 만드는 역할
* 암호 분석: [창]암호들을 분석하며 설계된 암호를 뚫는 과정

암호학은 1900BC 부터 시작되었다.
유명한 암호학은 **비밀키** **공개키** **HASH** algorithm이 있으며 이때 **비밀키**, **공개키**는 알고리즘이 비슷하다.

## secret-key algorithm
### Architecture
 평문 -> Encryption(Key) -> 암호문 -> Decription(Key) -> 평문
 
### 비밀키(대칭키)와 공캐키의 차이는 key의 차이가 있다.   
 
 1. 비밀키(대칭키) 암호  
- 하나의 비밀키를 양쪽(client & server)가 모두 같이 사용  
- 암호화와 복호화에 사용하는 키가 **같은** 암호화 알고리즘  
- 공개키와 비밀키를 별도로 가지는 것과 구별되는데, 이와 비교하면 계산속도가 빠르다는 장점   
- 비밀키 하나만 알아내면 암호화된 내용을 해독 가능 → 해커로부터 안전 X  
- 대킹키 암호는 암호화하는 단위에 따라 **스트림암호**와 **블록암호**로 나눌 수 있음  
- 스트림암호는 연속적인 **비트/바이트**를 계속해서 입력받아, 그에 대응하는 **암호화 비트/마이트**를 생성하는 방식  
- 블록암호는 **정해진 한 단위(블록)**을 입력받아 그에 대응하는 **암호화 블록**을 생성하는 방식   
- **블록암호**의 경우 적절한 운용모드를 조합하면 블록 단위보다 큰 입력을 처리할 수 있음.   
- 또한 스트림암호와 유사하게 지속적인 입력에 대해 동작할 수 있음. (대신 입출력 단위는 스트림암호보다 큰 블록 단위가 됨)  
- 대칭키 기법을 사용하는 암호 알고리즘 방식으로 DES, 3-DES, AES, SEED, ARIA, MASK 등이 있다.   

2. 공개키 암호 
- 비밀키 하나 만 가지는 대칭키 암호 방법과 달리, 공개키와 비밀키 두 개가 존재  
- 공개키 암호를 구성하는 알고리즘을 대칭키 암호 방식과 비교하여 비대칭 암호라고 불림  
- 암호화와 복호화에 사용하는 키가 서로 다름  
- 암호화할 때의 키는 공개키(public key), 복호화할 때의 키는 개인키(private key)  
- 공개키는 누구나 알 수 있지만, 그에 대응하는 비밀키는 키의 소유자만이 알 수 있어서 특정한 비밀키를 가지는 사용자만이 내용을 열어볼 수 있도록 하는 방식.  
 
 
### 세계대전
 
 비밀키 Algorithm은 세계대전 전후로 나뉘게 되었다., 세계대전 때 컴퓨터의 발명에 의해 암호 알고리즘의 해독이 비약적으로 늘어났기에..!  
 현대 비밀키는 컴퓨터의 해독으로도 바로 풀리지 않는 것들을 의미한다. 
 
### 비밀키 예시
#### Simple Substitution
 * 간단하게 대치를 이루어진다. (예, 알파벳 숫자를 3칸씩 미뤄서 저장한다.)
 * Caesar's Cipher Decryption
 ```
 tr 'a-z' 'vasdbasdfasdfasdfasf' < plaintext > cipertext
 tr 'vasdvasvasrasasaf' 'a-z' < ciphertext > plaintext_new
 ```
 * 일반적인 문자가 TAEIO 라고 하고  2문자씩 나눠진것이 bigrams, 3문자씩 나눠진것이 trigrams라고 한다. 
 * 이것들의 평문을 알아내는 것은 **Frequency Anaysis results**를 통해 전체적인 알파벳 사용 빈도를 확인하고, 가장 많이 사용했던 알파벳을 쉽게 예측할 수 있다.
 * bigrams, trigrams 다 동일하다.
 
 
#### DES( Data Encryption Standard )
 * 1970년도에 IBM에서 출시되었고, 미국에서 (백도어를 깔았다는 의심을 받으며) 출시되었다. 
 * DES는 block ciper으로 64bits들의 크기로 자른다. 하지만, DES는 56bit-key를 사용한다.
 * 30년이 지난 후 DES의 사용은 만료가 되었다. 그 이유는 2^56의 DES 연산이 30년동안 처리가 가능해졌기 떄문이다.
 
#### AES( Advanced Encryption Standard )
 * DES가 출시된 이후, 만료가 되었고 그것에서 더욱 발전된 상태의 암호표준이다.
 * 미국에서 만들었지만 공정성을 위해 공모전 결과 벨기에 코드가 채택되었다.
 * 128bit이상의 block size를 하고있다. 
 
#### ECB (Electronic Code book) mode
 * 데이터를 넣었던 그대로 복호화가 되는 것이다.
 * Pi = Pj 이면 Ci = Cj이다. 
 * 넣은대로 나오기 때문에 추축하기가 매우 쉬움 => 정보 노출에 용이함
 
 ```
 $ openssl enc -aes-128-ecb -e -in plain.txt -out cipher.txt -K 00112233445566778899AABBCCDDEEFF
 $ openssl enc -aes-128-ecb -d -in cipher.txt -out new_plain.txt -K 00112233445566778899AABBCCDDEEFF
 
 (-K (비밀키 16진수), -e( Encrytion 암호화) -d( Decriptrion 복호화) )
 ```
 * C = E(P,K)
 * P = D(C0,K)
#### CBC (Ciper Block Chainging) mode
 * Chain처럼 계속 이어지면서 xor게이트와 Initial Vector를 넣어서 완전히 다른 값으로 만들어버린다.  
 
 ```
 $ openssl enc -aes-128-ecb -e -in plain.txt -out cipher.txt -K 00112233445566778899AABBCCDDEEFF -iv 000102030405060708090a0b0c0d0e0f
 $ openssl enc -aes-128-ecb -d -in cipher.txt -out new_plain.txt -K 00112233445566778899AABBCCDDEEFF
 
 (-K (비밀키 16진수), -e( Encrytion 암호화) -d( Decriptrion 복호화) -iv: inital vector)
 ```
 
 * C = P xor E (IV,K)
 * P = C xor E (IV,K)
 
#### CTR (Counter Mode) mode
 * 병렬식으로 처리 되기에 CBC보다 더 최적화가된 방법이다. 

### 정보 보안(Imformation Security) 만족요건 3가지
* C Confidentiality(기밀성): 비밀키를 갖고 있는 사람이 정보에 접근해야만 한다.
* I Integrity(무결성): 변조가 되었을 때 server가 탐지할 수 있어야 한다.
** Alice->[E(M)=C]-> BOB Alice->C->C'-> BOB 탐지할 수 있을까? --> 무결성
* A Availability (가공성)
 
#### Authenticated Encryption
* CIA중 CI를 만족하는 것들

#### Padding
* Block cipher encryption modes는 plaintext를 블록으로 나누는데 이때 일정한 cipher의 block size로 나눈다.
* 이때 모든 데이터들이 자르려는 byte로 나눠지지 않기 떄문에 나머지것들을 채운뒤 같이 암호화가 진행된다.
* 만약 7bytes가 남겨진다면 07 07 07 07 07 07 07 07로 패딩이 된다.

#### Cryptography APIs

python에 암호화를 해주는 API가 있다.

```
from Crypto.Cipher import AES
from Crypro.Util import Padding
```


