# 이진 탐색 상한선(upper bound) 하한선 (lower bound)

## 이진 탐색

이진 탐색을 변형시킨 코드이다.

상한선(upper bound)
- 원하는 값보다 큰 값 중 가장 앞에 있는 값을 가져온다.

하한선(lower bound)
- 원하는 값보다 같거나 큰 값 중 가장 앞에 있는 값을 가져온다.

ex) 1 2 2 3 3 3 4 4 4 4

>이 배열에서 upper(3) 은 가장 앞의 4을 가리킨다. <br/>
lower(3)은 가장 앞에 있는 3은 가리킨다. <br/>
upper(0)과 lower(0)은 둘다 맨 앞의 1을 가리킨다. <br/>
upper(5)와 lower(5)는 마지막 4 뒤를 가리킨다. <br/>
인덱스 값 활용하기

<details>
<summary>이진 탐색 코드</summary>

```C
int BSearch(int ar[], int len, int target)
{
	int first=0;   // 탐색 대상의 시작 인덱스 값
	int last=len-1;   // 탐색 대상의 마지막 인덱스 값
	int mid; 

	while(first<=last)
	{
		mid = (first+last) / 2;   // 탐색 대상의 중앙을 찾는다. 

		if(target == ar[mid]) return mid;
		if(target<ar[mid]) last = mid-1;
		else first = mid+1;
		
	}
	return -1;   // 찾지 못했을 때 반환되는 값 -1
} 
```
</details>

<details>
<summary>upper bound lower bound 코드</summary>

```C
int upper_bound(int arr[], int left, int right, int target) {
	int mid;
	while (left < right) {
		mid = (left + right) / 2;
		if (arr[mid] <= target) left = mid + 1;
		else right = mid;
	}
	return right;
}

int lower_bound(int arr[], int left, int right, int target) {
	int mid;
	while (left < right) {
		mid = (left + right) / 2;
		if (arr[mid] < target) left = mid + 1;
		else right = mid;
	}
	return right;
}
```
</details>