# Checksum
체크섬은 데이터를 송신하는 중에 발생할 수 있는 오류를 검출하기 위한 값이다
전송할 데이터를 16Bits씩 나눠서 차례대로 더해가는 방법으로 생성한다.

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
