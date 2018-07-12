---
layout: post
title: 분할정복 기법을 이용한 quick sort 알고리즘
description: >
  렉토피아 정혜경 강사님의 강의를 들어면서
  quick sort 알고리즘에 대해서 정리해보았다.

tags: [neweins]
---

# quick sort 알고리즘

### 분할 정복(divde and conquer)이란?

- 하나의 문제를 작은 문제로 분할하여 문제를 해결하는 알고리즘
- 주로 재귀 함수(recursive Function)를 이용해서 구현하는 것이 일반적
- 빠른 실행이나 작은 문제 해결 순서를 선택을 위해 재귀 호출이 아닌 stack, queue 등의 자료 구조를 이용해서 구현하기도 함.

> 분할 정복 기법 설계 전략
> 1. 해결할 문제를 여러 개의 작은 부분으로 나눈다. - 분할(divide)
> 2. 나눈 작은 문제를 각각 해결한다. - 정복(conquer)
> 3. 해결된 해답을 모은다(optional) - 통합(combine)

### quick sort란 무엇인가?

- quick sort는 pivot 값을 중심으로 연속적으로 분할하며 정렬하는 기법
- pivot 값을 중심으로 pivot보다 작은 값을 왼쪽으로, pivot보다 큰 값을 오른쪽에 배열시키는 방식으로 sort가 완료될 때까지 반복적으로 수행한다.

### 알고리즘에 대해서 알아보자.

> 1. Sort 구간의 가장 우측의 값을 pivot으로 설정(설정 위치에 따라 성능 저하를 가져오기도 한다.)
> 2. 한 구간 안에서 다음을 반복적으로 수행한다.

>> 2-1. 구간 좌측부터 pivot보다 큰값을 i를 증가 시키면서 검사

>> 2-2. 구간 우측부터 pivot보다 작은 값을 j를 감소시키면서 검사

>> 2-3. i<j이면 i의 자리에 있는 값과 j의 자리에 있는 값을 swap한다.

> 3. i==j이거나 i>j이면 한 구간에 대한 교환이 완료된 것이므로 i 자리에 있는 값과 **pivot값을 swap한다.** (이때 pivot 값을 중심으로 좌측에는 pivot보다 작은 값이 위치하고 우측에는 pivot보다 큰 값들이 위치한다.)
>4. 이러한 분할 과정을 pivot값을 중심으로 나누어 좌측과 우측 두 구간에 대하여 똑같이 반복한다.(구간의 크기가 1이 될까지 재귀 호출함.)

![](/assets/img/20180711_213011.png)

~~~c

void quickSort(int *ary, int size);
void output(int *ary, int size);
void initArray(int *ary, int n);

int main()
{
	int dataList[10000];


	int startTime, endTime;
	int size = sizeof(dataList) / sizeof(dataList[0]);

	srand((unsigned int)time(NULL));

	initArray(dataList, size);

	startTime = clock();
	quickSort(dataList, size); 	/* 퀵 정렬 함수 호출 */
	endTime = clock();
	printf("\n\n[ 퀵 정렬  후 출력 ]\n");
	output(dataList, size);
	printf("퀵소트 알고리즘 실행 소요시간 : %d(millisecond)\n", (endTime-startTime));

	getchar();
	startTime = clock();
	quickSort(dataList, size); 	/* 퀵 정렬 함수 호출 */
	endTime = clock();
	printf("\n\n[ 퀵 정렬  후 출력 ]\n");
	output(dataList, size);
	printf("퀵소트 알고리즘 실행 소요시간 : %d(millisecond)\n", (endTime-startTime));
	getchar();
	return 0;
}
/*----------------------------------------------------------------------
함수명 및 기능 : quickSort() - 퀵 정렬 함수
전달인자: ary - 정렬할 데이터 배열의 시작주소
size - 소트할 데이터의 개수
리턴값: 없음
--------------------------------------------------------------------*/
void quickSort(int *ary, int size)
{
	int pivot, temp;
	int i, j;

	if(size<=1) return; // 구간값이 1이하이면 sort가 완료된 것 이므로 return

	pivot = ary[size-1];

	// TODO
	i=-1;
	j=size-1;

	while(1){
		while(ary[++i] < pivot);
		while(--j>=0 && ary[j] > pivot);
		if(i >=j)
			break;
		temp = ary[i];
		ary[i]=ary[j];
		ary[j]=temp;
	}
	temp=ary[i];
	ary[i]=ary[size-1];
	ary[size-1]=temp;
	quickSort(ary, i);// 좌측 소구간에 대한 퀵 정렬 재귀 호출
	quickSort(ary+i+1, size-i-1); // 우측 소구간에 대한 퀵 정렬 재귀 호출
}
/*------------------------------------------------------------------------
함수명 및 기능 : output() - 배열의 모든 원소 출력하기
전달인자: ary - 정렬할 데이터 배열의 시작주소
size - 데이터의 개수
리턴값: 없음
------------------------------------------------------------------------*/
void output(int *ary, int size)
{
	for(int i=0; i<size; i++)
	{
		printf(" %d ", ary[i]);
	}
	printf("\n");
}
/*------------------------------------------------------------------------
함수명 및 기능 : initArray() - 배열의 원소를 난수로 초기화
전달인자: ary - 초기화할 데이터 배열의 시작주소
size - 데이터의 개수
리턴값: 없음
------------------------------------------------------------------------*/
void initArray(int *ary, int n)
{
	int i;
	for(i=0; i<n; ++i)
		ary[i] = rand() % 1000 + 1;
}



~~~
