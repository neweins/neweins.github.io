---
layout: post
title: heap sort 알고리즘
description: >
  heap sort 알고리즘에 대해서 배워보자.
  렉토피아 정혜경 강사님의 강의를 듣고 정리해보았다.
tags: [neweins]
---

# heap sort 알고리즘

## heap의 개념

- heap은 우선 순위 큐(Priority Queue)의 일종으로 우선 순위가 높은 요소를 효율적으로 선택할 수 있는 자료구조이다.
- heap은 주로 tree구조로 구현하며 나무 구조에서 부모의 키 값을 두 자식의 키 값보다 크게 만들어 주면 된다.
- 즉, heap의 뿌리(root)는 전체 데이터 중에서 가장 우선 순위가 높은 자료이고 뿌리의 자식은 뿌리 보다 작은 두개의 자료로 구성하면 된다.

![](/assets/img/tree.png)

## Tree 구조를 배열로 구현하는 방법
tree 구조에서 층(layer)별로 tree를 순회하는 방법(Level order Traverse)으로 root 노드를 1번으로 시작해서 번호를 붙이면 다음과 같은 규칙대로 저장되게 된다.

1. 번호 j를 갖는 노드의 부모의 번호는 j/2이다.
2. 번호 j를 갖는 노드의 왼쪽 자식의 번호는 j*2이고, 오른쪽 자식은 j*2+1이다.
3. heap 자료의 개수 n의 절반(n/2)까지가 내부 노드이다.

> 내부 노드란? 단말 노드의 반대라는 뜻으로 자식 노드가 하나 이상 있는 노드를 의미한다.

- 배열 구조

|값 |사용안함|100|19|36|17|3|25|1|2|7|
|:-|:-|:-|:-|:-|:-|:-|:-|:-|:-|:-|:-|
|인덱스|[0]|[1]|[2]|[3]|[4]|[5]|[6]|[7]|[8]|[9]|

## Heap(배열로 구현된 heap)에서 데이터가 삭제되는 과정
1. heap의 최상단(root) 위치에서 데이터를 꺼낸 후
2. heap의 가장 마지막 위치의 데이터를 root 위치로 올리고
3. 마지막 위치에는 NIN값을 저장한 후
4. heap이 깨졌기 때문에 root 위치의 값에 대해 downHeap을 실시한다.
> down할 위치를 찾을때 자신의 두 자식 중 더 큰 값의 위치와 교환해야 함.

## 데이터 삭제 과정
1. 9가 삭제되면
2. 마지막 4가 root로 온다.
3. 4를 downHeap 실시힌다.
![](/assets/img/tree2.png)
![](/assets/img/heap_data_remove.png)

## 코드 구현

### Heap 구현에 사용되는 데이터 형
~~~c
/*heap 관리 구조체*/
typedef struct _heap{
  int* heap;  //배열 데이타의 시작주소
  int size;
  int count; //heap에 저장되어 있는 데이터의 수
}Heap;
~~~

### Heap 기능 함수 목록

~~~c
enum BOOL {FALSE, TRUE};
typedef struct _heap{
  int* heap;
  int size;
  int count; //heap에 저장되어 있는 데이터의 수
}Heap;

BOOL createHeap(Heap *hPtr, int size);  /*힙 생성 함수*/
void destroyHeap(Heap *hPtr); /*힙 소멸 함수*/
BOOL deleteDownHeap(Heap* hPtr, int* getData);  /*힙에 데이터 하나를 삭제*/
void downHeap(Heap *hPtr, int position);  /*지정 노드를 위치에 맞게 down시킴*/
void printHeap(const Heap* hPtr); /*힙의 데이터 출력 상위->하위 방향*/
BOOL isHeapEmpty(Heap *hPtr); /*힙이 완전히 비어있는가 검사*/
BOOL isHeapFull(Heap *hPtr);  /*힙이 꽉 차 있는가 검사*/
~~~

### test 함수 구현
~~~c
int main()
{
  int ary[9] = {1,2,3,4,5,6,7,8,9};
  int i;
  int size = sizeof(ary)/sizeof(ary[0]);
  Heap = heap;
  int getData;
  BOOL res;

  createHeap(&heap, size);
  for(i=0; i<size; ++i)
    heap.heap[i+1] = ary[i];
  heap.count = size;

  for(i=heap.size/2; i>=1; --i){
    downHeap(&heap, i);
  }

  printf("print heap : ");
  printHeap(&heap);

  printf("delete heap : ");


}
~~~

### createHeap 함수

1. 설계

~~~c
/*----------------------------------------------------------------
Function name : createHeap() - 힙 생성 함수
Parameters : hPtr - 힙 구조체의 주소
             size - 힙의 크기(저장 가능한 데이터의 개수)
Returns    : 생성 성공하면 TRUE, 실패하면 FALSE
----------------------------------------------------------------*/
BOOL createHeap(Heap *hPtr, int size)
{
  // hPtr 포인터 NULL check

  // 실제 힙의 크기보다 1크게 동적 할당함
  // 0번 방은 보초값을 저장하는 용도로 사용

  //힙 메모리 할당 실패시 FALSE 리턴

  //힙의 size 멤버 초기화
  //힙의 count 멤버 초기화
  //힙의 0번 방은 보초값(INT_MAX) 저장함

}
~~~

2. 구현

~~~c
BOOL createHeap(Heap *hPtr, int size)
{
  if(hPtr == NULL)  
    return FALSE;

  hPtr->heap = (int*)calloc(size+1, sizeof(int));
  if(hPtr->heap ==NULL)
    return FALSE;

  hPtr->size = size;
  hPtr->count =0;
  hPtr->heap[0]=INT_MAX;
  return TRUE;
}
~~~

### isHeapEmpty 함수

~~~c
/*----------------------------------------------------------------
Function name : isHeapEmpty() - 힙 완전히 비어있는가 검사
Parameters : hPtr - 힙 구조체의 주소
Returns    : 완전히 비어 있으면 TRUE, 비어있지 않으면 FALSE
----------------------------------------------------------------*/
BOOL isHeapEmpty(Heap *hPtr)
{
  if(hPtr == NULL){
    return FALSE;
  }

  if(hPtr-> count ==0){
    return TRUE;
  }
  else{
    return FALSE;
  }
}
~~~

### isHeapFull 함수

~~~c
/*----------------------------------------------------------------
Function name : isHeapFull() - 힙이 차 있는지 검사
Parameters : hPtr - 힙 구조체의 주소
Returns    : 차 있으면 TRUE, 차 있지 않으면 FALSE
----------------------------------------------------------------*/
BOOL isHeapFull(Heap *hPtr)
{
  if(hPtr == NULL){
    return FALSE;
  }

  if(hPtr-> count == hPtr->size){
    return TRUE;
  }
  else{
    return FALSE;
  }
}
~~~

### deleteDownHeap 함수

1. 설계

~~~c
/*----------------------------------------------------------------
Function name : deleteDownHeap() - 힙에 데이터 하나를 삭제
Parameters : hPtr - 힙 구조체의 주소
             getData - 힙에 꺼낸 데이터 저장 변수의 주소
Returns    : 성공적으로 삭제하면 TRUE, 실패하면 FALSE
----------------------------------------------------------------*/
BOOL deleteDownHeap(Heap *hPtr, int* getData)
{
  //힙이 비어있으면 FALSE 리턴
  //힙이 비어있지 않으면 다음 내용 실행
    //1번 방의 값(가장 큰 값)을 getData가 가리키는 곳에 저장
    //(0번 방은 보초값으로 실제 데이터 아님)
    //힙의 말단 노드를 root 위치로 올림
    //root 위치로 옮겨간 데이터가 있던 곳에 보초값(INT_MIN) 저장 count (데이터의 개수) 하나 감소
    //downHeap()함수를 이용하여  root값에 대해 downHeap 실시

}
~~~

2. 구현

~~~c
BOOL deleteDownHeap(Heap *hPtr, int* getData)
{
  if(hPtr == NULL){
    return FALSE;
  }

  if(isHEapEmpty(hPtr)){
    return FALSE;
  }

  * getData = hPtr->heap[1]; //1번 방의 값(가장 큰 값)을 getData가 가리키는 곳에 저장
  hPtr->heap[1] = hPtr->heap[hPtr->count]; //힙의 말단 노드를 root 위치로 올림
  hPtr->heap[hPtr->count] = INT_MIN;
  --hPtr->count;

  downHeap(hPtr, 1);
  return TRUE;
}
~~~

### downHeap 함수

1. 설계

~~~c
/*----------------------------------------------------------------
Function name : downHeap() - 지정 노드를 위치에 맞게 down 시킴
Parameters : hPtr - 힙 구조체의 주소
             position - 하강(down heap) 시킬 데이터의 위치
Returns    : 없음
----------------------------------------------------------------*/
BOOL downHeap(Heap *hPtr, int* getData)
{
  //hPtr 포인터 NULL check

  //반복문을 이용하여 하강(down heap)시킬 데이터가 들어갈 위치를 찾음
    //down할 위치를 찾을 때 자신의 두 자식 중 더 큰 값의 위치와 교환해야 함.

  // 찾은 하강 위치에 하강데이터 저장

}
~~~

2. 구현

~~~c
void downHeap(Heap *hPtr, int* getData)
{
  int childPosition;
  int downData;

  if(hPtr == NULL){
    return;
  }

  downData = hPtr->heap[position];

  while(position <= hPtr->count /2) //parentPosition에 내부 노드이면 down시킴
  {
    childPosition = position << 1;  //position*= 2와 같은 연산으로 왼쪽 자식
    if(hPtr->heap[childPosition] < hPtr->heap[childPosition+1]){
      ++childPosition;  //오른쪽 자식의 위치 선책
    }
    if(downData >= hPtr->>heap[childPosition])  //더 이상 하강할 필요가 없으면 탈출
      break;
    hPtr->heap[position] = hPtr->heap[childPosition];
    position = childPosition; //자식값의 위치로 position위치를 수정한다.
  }
  hPtr->heap[position] = downData;  //정해진 하강위치에 하강 데이터 저장
}
~~~
