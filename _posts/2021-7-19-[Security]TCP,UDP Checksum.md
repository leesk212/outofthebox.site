---
tags: security-defense network
toc: True
---
# Checksum
* 체크섬은 데이터를 송신하는 중에 발생할 수 있는 오류를 검출하기 위한 값이다 전송할 데이터를 16Bits씩 나눠서 차례대로 더해가는 방법으로 생성한다.  
![image](https://user-images.githubusercontent.com/67637935/126106222-28d91a17-b333-418f-b304-e053dbbaf00f.png)
* Sender side - Checksum Createion  
  * Break the original message in to 'k' number of blocks with 'n' bits in each block.  
![image](https://user-images.githubusercontent.com/67637935/126106391-f1dfb35e-c6bd-426a-a031-3c0736348cb1.png)
  * Sum all the 'k' data blocks  
![image](https://user-images.githubusercontent.com/67637935/126106492-4a11e489-877c-4cea-891c-7e7c77a05444.png)
  * Add the carray to the sum, if any  
![image](https://user-images.githubusercontent.com/67637935/126106552-b8b0bc8b-1d08-4f97-8001-88da4608ab15.png)
  * Do 1's complement to the sum = Checksum  
![image](https://user-images.githubusercontent.com/67637935/126106610-988f4a85-3fce-4892-9453-4854dbed72e6.png)
  * CHECKSUM 11011010 
* Recivere side - Checksum Validation
  * Get Data from Sender [Checksum][SectionK...]  
![image](https://user-images.githubusercontent.com/67637935/126106895-cd219dd9-4879-4c4e-9d69-72b4075cc8e3.png)
  * Calculate all data value with checksum and carray --> to be 1 
![image](https://user-images.githubusercontent.com/67637935/126107008-5b769afc-30ad-4794-9534-1a11e775e0ec.png)
# Checksum code 'C'

```c
unsigned short in_cksum(unsinged short *ptr, int nbytes);

// IP헤더의 포인터와 길이를 인자로 받음
unsigned short in_cksum(unsinged short *ptr, int nbytes)
{
register long sum;
u_short oddbyte;
register u_short answer;  // 돌려 줄 값

sum = 0;  // sum 값을 0으로 초기
while(nbytes > 1) { // 읽어올값이 남아있으면
sum += *ptr++; // 포인터 하나씩 증가하면서 sum값에 더함
nbytes -= 2; // 그리고 nbytes에서는 2를 뺌
}

if(nbytes == 1) { // 남은 읽어 올 값이 홀수 이면
oddbyte = 0; // 홀수 바이트를 0으로 세팅하고
*((u_char *)&oddbyte) = *(u_char *)ptr;
sum += oddbyte; // 그것도 더해줌
}

// 상위 16바이트와 하위 16바이트의 합
sum = (sum >> 16) + (sum & 0xffff); 
// 올라 온 값이 있으면 그것도 더해줌
sum += (sum >> 16);
answer = ~sum; // 전체 비트반전
return (answer); // 만들어진 값을 리턴
} // TCP체크썸 계산 함수 끝

```


# Reference
* https://evan-moon.github.io/2019/11/10/header-of-tcp/#checksum
* https://limjunho.github.io/2021/06/05/UDP-cksum.html
* https://github.com/vozlt/check-protocol/blob/master/icmp_traceroute.c
* https://gist.github.com/david-hoze/0c7021434796997a4ca42d7731a7073a
* https://www.youtube.com/watch?v=AtVWnyDDaDI
