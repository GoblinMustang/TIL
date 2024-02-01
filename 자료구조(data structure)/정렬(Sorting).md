# 정렬

## 단순한 정렬 알고리즘
* 버블 정렬(Bubble Sort)
* 선택 정렬(Selection Sort)
* 삽입 정렬(Insertion Sort)

단순 정렬 알고리즘들은 구현이 쉬우나, 성능이 만족스럽지 못 하다.
O(n^2) 수준
선택 정렬과 삽입 정렬의 차이점이 헷갈릴 수 있는데, 선택 정렬은 인덱스 차례대로 그 인덱스에 들어갈 데이터를 선택하는 정렬이고, 삽입 정렬은 정렬된 부분에 데이터를 하나씩 삽입하고 정렬하는 알고리즘이다.

## 복잡하지만 효율적인 정렬 알고리즘
* 힙 정렬(Heap Sort)

    - 힙을 이용한 정렬로 단순한 정렬 알고리즘에 비해 구현이 조금 더 어려우나, 만족스러운, 좋은 성능을 가진다.
    - O(nlogn) 수준.
    ##

* 병합 정렬(Merge Sort)
    - 이동연산, 비교연산 빅-오 둘다 O(nlogn)
    - 배열로 구현하면 임시 메모리가 필요하다는 단점 있음.
    - 연결리스트의 경우는 임시 메모리 필요 X -> 배열에 비해 더 좋은 성능 기대 가능



<details>
<summary>병합 정렬 구현 코드(출처: 윤성우의 열혈 자료구조)</summary>

<!-- summary 아래 한칸 공백 두어야함 -->
## 병합 정렬(Merge Sort)
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


* 퀵 정렬(Quick Sort)

