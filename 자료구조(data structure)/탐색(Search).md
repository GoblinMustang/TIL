# 탐색

## 탐색의 이해

* 효율적인 탐색을 위해선 '어떻게 찾을까'만 고민해선 안 된다.
* '효율적인 탐색을 위한 저장 방법'을 우선 고민

    - 효율적인 탐색이 가능한 대표적인 자료구조는 트리(이진트리)

## 보간 탐색(Interpolation Search)
>이진 탐색과 보간 탐색 모두 정렬이 완료된 데이터를 대상으로 탐색을 진행하는 알고리즘이다.
```
 이진 탐색  무조건 중간에 위치한 데이터를 탐색 위치로 결정
 보간 탐색  대상에 비례하여 탐색의 위치를 결정
```
### 보간 탐색은 '전화번호부'나 '사전'에 비유된다.
- 전화번호부에서 사람의 전화번호를 찾을 때, 이진 탐색처럼 중앙을 펼치지는 않는다.
- 사람의 초성 인덱스를 보고 탐색 위치를 결정한다.
> 이처럼 보간 탐색은 데이터가 '직선의 형태로 비례하여' 저장되어 있다고 가정한 후 보간 탐색의 탐색 위치를 결정한다.

<details>
<summary>보간 탐색 코드(출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

int ISearch(int ar[], int first, int last, int target) {
	int mid;	// 이진 탐색을 변경한 코드라 mid라고 네이밍 되었음.

	if (ar[first] > target || ar[last] < target) return -1;

	mid = ((double)(target - ar[first]) / (ar[last] - ar[first]) *
		(last - first) + first);    // 보간 탐색 핵심 코드

	if (ar[mid] == target) return mid;
	else if (target < ar[mid])
		return ISearch(ar, first, mid - 1, target);
	else
		return ISearch(ar, mid + 1, last, target);
}

int main(void) {
	int arr[] = { 1, 3, 5, 7 ,9 };
	int idx;

	idx = ISearch(arr, 0, sizeof(arr) / sizeof(int) - 1, 2);
	if (idx == -1) printf("탐색 실패\n");
	else printf("타겟 저장 인덱스: %d\n", idx);
}
```
</details>

## 탐색 키(Search Key) 와 탐색 데이터(Search Data)
> 도서관에서 책을 찾는 것처럼 책의 고유 번호(탐색 키)를 통해 탐색을 하나, 책의 고유 번호(탐색 키)를 찾는 것이 아니라 책(탐색 데이터)를 찾는 것이다. 이를 명확히 인지하며 탐색을 공부할 것.

## 이진 탐색 트리(Binary Search Tree)

### 이진 탐색 트리의 이해
```
이진 탐색 트리 = 이진 트리 + 데이터의 저장 규칙

- 저장 규칙이 곧 탐색 규칙
```
| 자료구조 | 위치정보(경로) | 데이터의 수| 거치는 노드의 수|
|:-:|:-:|:-:|:-:|
| 연결 리스트| O | 10억 개| 최악의 경우 10억 개
| 이진 탐색 트리 | O | 10억 개| 평균 30개 미만

## 균형 잡힌 이진 탐색 트리: AVL 트리의 이해
### 균형 인수(Balance Factor)
균형 인수 = | 왼쪽 서브 트리의 높이 - 오른쪽 서브 트리의 높이 |
> 균형 인수가 | 2 | 보다 큰 노드가 있다면, 트리는 불균형한 상태다.
>> 리밸런싱이 필요한 상태!