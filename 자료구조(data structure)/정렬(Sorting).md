# 정렬

## 단순한 정렬 알고리즘
* 버블 정렬(Bubble Sort) 

<details>
<summary>버블 정렬 코드(출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

void BubbleSort(int arr[], int n)
{
	int i, j;
	int temp;

	for(i=0; i<n-1; i++)
	{
		for(j=0; j<(n-i)-1; j++)
		{
			if(arr[j] > arr[j+1])
			{
				temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
			}
		}
	}
}


int main(void)
{
	int arr[4] = {3, 2, 4, 1};
	int i;

	BubbleSort(arr, sizeof(arr)/sizeof(int));

	for(i=0; i<4; i++)
		printf("%d ", arr[i]);

	printf("\n");
	return 0;
}
```
</details>

* 선택 정렬(Selection Sort)
<details>
<summary>선택 정렬 코드(출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

void SelSort(int arr[], int n)
{
	int i, j;
	int maxIdx;
	int temp;

	for(i=0; i<n-1; i++)
	{
		maxIdx = i;    // 정렬 순서상 가장 앞서는 데이터의 index

		for(j=i+1; j<n; j++)   // 최소값 탐색
		{
			if(arr[j] < arr[maxIdx])
				maxIdx = j;
		}

		/* 교환 */
		temp = arr[i];
		arr[i] = arr[maxIdx];
		arr[maxIdx] = temp;
	}
}


int main(void)
{
	int arr[4] = {3, 4, 2, 1};
	int i;

	SelSort(arr, sizeof(arr)/sizeof(int));

	for(i=0; i<4; i++)
		printf("%d ", arr[i]);

	printf("\n");
	return 0;
}
```
</details>

* 삽입 정렬(Insertion Sort)
<details>
<summary>삽입 정렬 코드(출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

void InserSort(int arr[], int n)
{
	int i, j;
	int insData;

	for(i=1; i<n; i++)
	{
		insData = arr[i];   // 정렬 대상을 insData에 저장

		for(j=i-1; j>=0 ; j--)
		{
			if(arr[j] > insData) 
				arr[j+1] = arr[j];    // 비교 대상 한 칸 뒤로 밀기
			else
				break;   // 삽입 위치 찾았으니 탈출!
		}

		arr[j+1] = insData;  // 찾은 위치에 정렬 대상 삽입!
	}
}


int main(void)
{
	int arr[5] = {5, 3, 2, 4, 1};
	int i;

	InserSort(arr, sizeof(arr)/sizeof(int));

	for(i=0; i<5; i++)
		printf("%d ", arr[i]);

	printf("\n");
	return 0;
}
```
</details>

단순 정렬 알고리즘들은 구현이 쉬우나, 성능이 만족스럽지 못 하다.
O(n^2) 수준
선택 정렬과 삽입 정렬의 차이점이 헷갈릴 수 있는데, 선택 정렬은 인덱스 차례대로 그 인덱스에 들어갈 데이터를 선택하는 정렬이고, 삽입 정렬은 정렬된 부분에 데이터를 하나씩 삽입하고 정렬하는 알고리즘이다.

## 복잡하지만 효율적인 정렬 알고리즘
* 힙 정렬(Heap Sort)

    - 힙을 이용한 정렬로 단순한 정렬 알고리즘에 비해 구현이 조금 더 어려우나, 만족스러운, 좋은 성능을 가진다.
    - O(nlogn) 수준.

<details>
<summary>힙 정렬 코드(출처: 윤성우의 열혈 자료구조)</summary>

<details>
<summary>main.c</summary>

```C
#include <stdio.h>
#include "UsefulHeap.h"

int PriComp(int n1, int n2)
{
	return n2-n1;
//	return n1-n2;
}

void HeapSort(int arr[], int n, PriorityComp pc)
{
	Heap heap;
	int i;

	HeapInit(&heap, pc);

	// 정렬 대상을 가지고 힙을 구성한다.
	for(i=0; i<n; i++)
		HInsert(&heap, arr[i]);

	// 순서대로 하나씩 꺼내서 정렬을 완성한다.
	for(i=0; i<n; i++)
		arr[i] = HDelete(&heap);
}

int main(void)
{
	int arr[4] = {3, 4, 2, 1};
	int i;

	HeapSort(arr, sizeof(arr)/sizeof(int), PriComp);

	for(i=0; i<4; i++)
		printf("%d ", arr[i]);

	printf("\n");
	return 0;
}

```
</details>
<details>
<summary>UsefulHeap.c</summary>

```C
#include "UsefulHeap.h"

void HeapInit(Heap * ph, PriorityComp pc)
{
	ph->numOfData = 0;
	ph->comp = pc;
}

int HIsEmpty(Heap * ph)
{
	if(ph->numOfData == 0)
		return TRUE;
	else
		return FALSE;
}

int GetParentIDX(int idx) 
{ 
	return idx/2; 
}

int GetLChildIDX(int idx) 
{ 
	return idx*2; 
}

int GetRChildIDX(int idx) 
{ 
	return GetLChildIDX(idx)+1; 
}

int GetHiPriChildIDX(Heap * ph, int idx)
{
	if(GetLChildIDX(idx) > ph->numOfData)
		return 0;

	else if(GetLChildIDX(idx) == ph->numOfData)
		return GetLChildIDX(idx);

	else
	{
	//	if(ph->heapArr[GetLChildIDX(idx)].pr 
	//				> ph->heapArr[GetRChildIDX(idx)].pr)
		if(ph->comp(ph->heapArr[GetLChildIDX(idx)], 
					ph->heapArr[GetRChildIDX(idx)]) < 0)
			return GetRChildIDX(idx);
		else
			return GetLChildIDX(idx);
	}
}

void HInsert(Heap * ph, HData data)
{
	int idx = ph->numOfData+1;

	while(idx != 1)
	{
	//	if(pr < (ph->heapArr[GetParentIDX(idx)].pr))
		if(ph->comp(data, ph->heapArr[GetParentIDX(idx)]) > 0)
		{
			ph->heapArr[idx] = ph->heapArr[GetParentIDX(idx)];
			idx = GetParentIDX(idx);
		}
		else
		{
			break;
		}
	}
	
	ph->heapArr[idx] = data;
	ph->numOfData += 1;
}

HData HDelete(Heap * ph)
{
	HData retData = ph->heapArr[1];
	HData lastElem = ph->heapArr[ph->numOfData];

	int parentIdx = 1;
	int childIdx;

	while(childIdx = GetHiPriChildIDX(ph, parentIdx))
	{
	//	if(lastElem.pr <= ph->heapArr[childIdx].pr)
		if(ph->comp(lastElem, ph->heapArr[childIdx]) >= 0)
			break;

		ph->heapArr[parentIdx] = ph->heapArr[childIdx];
		parentIdx = childIdx;
	}

	ph->heapArr[parentIdx] = lastElem;
	ph->numOfData -= 1;
	return retData;
}
```
</details>
<details>
<summary>UsefulHeap.h</summary>

```C
#ifndef __USEFUL_HEAP_H__
#define __USEFUL_HEAP_H__

#define TRUE	1
#define FALSE	0

/*** Heap의 정의 ****/
#define HEAP_LEN	100

typedef int HData;

// d1의 우선순위가 높다면 0보다 큰 값
// d2의 우선순위가 높다면 0보다 작은 값
// d1과 d2의 우선순위가 같다면 0을 반환
typedef int PriorityComp(HData d1, HData d2);

typedef struct _heap
{
	PriorityComp * comp;
	int numOfData;
	HData heapArr[HEAP_LEN];
} Heap;

/*** Heap 관련 연산들 ****/
void HeapInit(Heap * ph, PriorityComp pc);
int HIsEmpty(Heap * ph);

void HInsert(Heap * ph, HData data);
HData HDelete(Heap * ph);

#endif
```
</details>
</details>

##

* 병합 정렬(Merge Sort)
    - 이동연산, 비교연산 빅-오 둘다 O(nlogn)
    - 배열로 구현하면 임시 메모리가 필요하다는 단점 있음.
    - 연결리스트의 경우는 임시 메모리 필요 X -> 배열에 비해 더 좋은 성능 기대 가능

<details>
<summary>병합 정렬 코드 (출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>
#include <stdlib.h>

void MergeTwoArea(int arr[], int left, int mid, int right) {
	int fIdx = left;
	int rIdx = mid + 1;
	int i;

	int* sortArr = (int*)malloc(sizeof(int)*(right + 1));
	int sIdx = left;

	while (fIdx <= mid && rIdx <= right) {
		if (arr[fIdx] <= arr[rIdx]) {
			sortArr[sIdx] = arr[fIdx++];
		}
		else {
			sortArr[sIdx] = arr[rIdx++];
		}
		sIdx++;
	}
	
	if (fIdx > mid) {
		for (i = rIdx; i <= right; i++, sIdx++) {
			sortArr[sIdx] = arr[i];
		}
	}
	else {
		for (i = fIdx; i <= mid; i++, sIdx++) {
			sortArr[sIdx] = arr[i];
		}
	}

	for (i = left; i <= right; i++) {
		arr[i] = sortArr[i];
	}
	free(sortArr);
}

void MergeSort(int arr[], int left, int right) {
	int mid;

	if (left < right) {
		mid = (left + right) / 2;
		MergeSort(arr, left, mid);
		MergeSort(arr, mid + 1, right);

		MergeTwoArea(arr, left, mid, right);
	}
}

int main(void) {

	int arr[7] = { 3, 2, 4, 1, 7, 6, 5 };
	int i;

	MergeSort(arr, 0, sizeof(arr) / sizeof(int) - 1);

	for (i = 0; i < 7; i++)
		printf("%d ", arr[i]);
	printf("\n");

	return 0;
}

```

</details>
	

##
* 퀵 정렬(Quick Sort)
	- 빅-오는 O(nlogn)이지만, 다른 O(nlogn)인 정렬 알고리즘에 비해 더 빠른 모습을 보여준다.

<details>
<summary>퀵 정렬 코드 (출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

void Swap(int arr[], int idx1, int idx2) {
	int temp = arr[idx1];
	arr[idx1] = arr[idx2];
	arr[idx2] = temp;
}

int MedianOfThree(int arr[], int left, int right) {
	int samples[3] = { left, (left + right) / 2, right };

	if (arr[samples[0]] > arr[samples[1]])
		Swap(samples, 0, 1);
	if (arr[samples[1]] > arr[samples[2]])
		Swap(samples, 1, 2);
	if (arr[samples[0]] > arr[samples[1]])
		Swap(samples, 0, 1);

	return samples[1];
}

int Partition(int arr[], int left, int right) {
	int pIdx = MedianOfThree(arr, left, right);
	int pivot = arr[pIdx];
	int low = left + 1;
	int high = right;

	Swap(arr, left, pIdx);

	//printf("피벗: %d\n", pivot);

	while (low <= high) {
		while (pivot >= arr[low] && low <= right)
			low++;
		while (pivot <= arr[high] && high >= (left + 1))
			high--;
		if (low <= high)
			Swap(arr, low, high);
	}
	Swap(arr, left, high);
	return high;
}

void QuickSort(int arr[], int left, int right) {
	if (left < right) {
		int pivot = Partition(arr, left, right);
		QuickSort(arr, left, pivot - 1);
		QuickSort(arr, pivot + 1, right);
	}
}

int main(void) {

	int arr[15] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 };
	//int arr[3] = {3, 3, 3};
	int len = sizeof(arr) / sizeof(int);
	int i;

	QuickSort(arr, 0, (sizeof(arr) / sizeof(int)) - 1);

	for (i = 0; i < len; i++) {
		printf("%d ", arr[i]);
	}
	printf("\n");

	return 0;
}
```
#### 보통 피벗을 가장 앞 세 데이터 중 중간 값 데이터로 지정하거나 가장 왼쪽, 가장 오른쪽, 중앙 값 중 중간 값으로 지정해서 극단적 확률을 줄인다. <br/> 이 경우 피벗을 정하는 연산이 더 추가되지만, 더 안정적인 성능을 보여준다. <br/> 퀵 정렬의 평균적으로 최선에 가까운 성능을 평균적으로 보여주기에, 예외적으로 빅-오를 최악의 경우로 얘기하지 않고 평균적인 빅-오로 본다. <br/> 심지어 동일한 빅-오를 가지는 다른 정렬 알고리즘보다 평균적으로 빠르다.
</details>
	
##
* 기수 정렬(Radix Sort)
	- 데이터를 구성하는 기본 요소, 기수를 이용해서 정렬을 진행하는 방식
	- 정렬 알고리즘의 한계로 알려진 O(nlogn)을 뛰어 넘을 수 있다.
	- 적용할 수 있는 대상이 매우 제한적이다. (정렬 데이터의 길이가 같아야 함)
	- 비교 연산을 진행하지 않는다.
	- 빅-오는 O(ln) (l은 정렬 데이터의 길이, n은 정렬 데이터의 개수) 거의 O(n)이라고 봐도 된다.
	- LSD(Least Significant Digit)은 덜 중요한 자리, 첫 번째 자리수부터 정렬하는 방식(일반적 방식)
	- MSD(Most Significant Digit)은 가장 중요한, 마지막 자리수부터 정렬하는 방식
<details>
<summary>기수 정렬 코드 (출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>
#include "ListBaseQueue.h"	// 큐 헤더파일

#define BUCKET_NUM	10

void RadixSort(int arr[], int num, int maxLen) {
	Queue buckets[BUCKET_NUM];
	int bi;
	int pos;
	int di;
	int divfac = 1;
	int radix;

	for (bi = 0; bi < BUCKET_NUM; bi++) {
		QueueInit(&buckets[bi]);
	}

	for (pos = 0; pos < maxLen; pos++) {
		for (di = 0; di < num; di++) {
			radix = (arr[di] / divfac) % 10;
			Enqueue(&buckets[radix], arr[di]);
		}
		for (bi = 0, di = 0; bi < BUCKET_NUM; bi++) {
			while (!QIsEmpty(&buckets[bi]))
				arr[di++] = Dequeue(&buckets[bi]);
		}
		divfac *= 10;
	}
}

int main(void) {
	int arr[] = { 13, 212, 14, 7141, 10987, 6, 15 };

	int len = sizeof(arr) / sizeof(int);
	int i;

	RadixSort(arr, len, 5);

	for (i = 0; i < len; i++)
		printf("%d ", arr[i]);
}
```
</details>

##