---

layout: post
title: [문제해결] Data reordering

description: >
중앙 집중 제어를 위해서 각 장치에서 들어오는 4바이트의 정보를 기존에 구축되어있는 서버측 데이터 형태에 맞도록 정보의 재배치가 필요하다.


tags: [neweins]

---

# [문제해결] Data reordering

현재 장치에서 수신되는 4바이트의 정보 데이터는 다음과 같다.
![](/assets/img/dataReorder_1.jpg)

이미 구축된 서버에서 원하는 4바이트의 정보는 데이터는 다음과 같다.
![](/assets/img/dataReorder_2.jpg)

즉 장치에서 수신되 4바이트의 데이터를 서버측 으로 전달하기 위해서는 다음과 같은 비트 정보의 재배치가 필요해진다.
![](/assets/img/dataReorder_3.png)

다음 그림은 실제 변경의 예를 보인다.
장치로부터 수신된 데이터가 16진수 0x491A8AC4일 경우
![](/assets/img/dataReorder_4.jpg)

변환된 데이터는 다음과 같이 16진수 0x45494C13이 된다.
![](/assets/img/dataReorder_5.jpg)

같은 항목내에서의 비트의 순서는 변경되지 않는다고 할 때 정보를 재 구성하는 아래와 같은 Make_Data함수를 설계 하시오.


[16진수->2진수 변환 코드 참조](https://neweins.github.io/2018/08/25/(알고리즘)16진수-2진수변환/)

~~~c

#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

using namespace std;

int fourDigits[16][4] ={
	{0,0,0,0}, 
	{0,0,0,1},
	{0,0,1,0},
	{0,0,1,1},
	{0,1,0,0},
	{0,1,0,1},
	{0,1,1,0},
	{0,1,1,1},
	{1,0,0,0},
	{1,0,0,1},
	{1,0,1,0},
	{1,0,1,1},
	{1,1,0,0},
	{1,1,0,1},
	{1,1,1,0},
	{1,1,1,1}
};

int digits[32];
int newDigits[32];

unsigned long long int Make_Data(unsigned int rcv);

// 작성할 함수
int setDigits(int idx, char value)
{
	int val =0;
	switch(value){
		case '0': val =0;
		break;
		case '1': val =1;
			break;
		case '2': val = 2;
			break;
		case '3': val = 3;
			break;
		case '4': val = 4;
			break;
		case '5': val = 5;
			break;
		case '6': val = 6;
			break;
		case '7': val = 7;
			break;
		case '8': val = 8;
			break;
		case '9': val = 9;
			break;
		case 'A': val = 10;
			break;
		case 'B': val =11;
			break;
		case 'C': val =12;
			break;
		case 'D': val =13;
			break;
		case 'E': val = 14;
			break;
		case 'F': val = 15;
			break;
		default:
			val =0;
			
	}

	for(int i=0; i< 4; ++i){
		digits[idx*4+i] = fourDigits[val][i];
	}
}

void reorderDigits(int orgIdx, int destIdx, int size)
{
	orgIdx = 31-orgIdx;
	destIdx = 31- destIdx;
	for(int i=orgIdx; i< orgIdx+size; ++i){
		newDigits[destIdx++] = digits[i];
	}
}
unsigned long long int Make_Data(unsigned int rcv)
{	
	char buf[8+1];
	sprintf(buf, "%X", rcv);
	// printf("\n\n%s\n\n", buf);
	
	char strRcv[8+1]={'0',};
	int startPnt = 8-strlen(buf);
	strncpy(&strRcv[startPnt],buf, strlen(buf));
		
	for(int i=7; i>= 0; --i){
		setDigits(i, strRcv[i]);
	}
	//for(int i=0; i<32; ++i)	printf("%d", digits[i]);
	//printf("\n");
	reorderDigits(31, 23, 6);
	reorderDigits(25,15,3);
	reorderDigits(22,3,4);
	reorderDigits(18, 27, 4);
	reorderDigits(14, 7, 4);
	reorderDigits(10,17,2);
	reorderDigits(8, 12, 5);
	reorderDigits(3, 31, 4);
	//for(int i=0; i<32; ++i)	printf("%d", newDigits[i]);
	//printf("\n");
	unsigned long long int sum = 0;
	for(int i=31; i>=0; --i){
		sum += newDigits[31-i]*pow(2,i);
	}
	//printf("\nsum = %ld\n", pow(2, 30));
	
	return sum;
}

int main(void)
{
	unsigned long int rcv=0,sol;
	
	// 입력 받는 부분
	cin >> hex >> uppercase >> rcv;
		
	sol = Make_Data(rcv);

	cout << hex << uppercase << sol;
}


~~~