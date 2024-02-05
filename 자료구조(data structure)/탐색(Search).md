# 탐색(Search)

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

<details>
<summary>이진 탐색 트리 코드(출처: 윤성우의 열혈 자료구조)</summary>

<details>
<summary>BinarySearchTree.c</summary>

>AVL 트리에 이 코드르 적용하려면 삽입과 삭제 함수에
*pRoot = Rebalance(*pRoot); 를 마지막에 추가해야 함.
```C
#include <stdio.h>
#include <stdlib.h>
#include "BinaryTree3.h"
#include "BinarySearchTree2.h"

void BSTMakeAndInit(BTreeNode ** pRoot)
{
	*pRoot = NULL;
}

BSTData BSTGetNodeData(BTreeNode * bst)
{
	return GetData(bst);
}

void BSTInsert(BTreeNode ** pRoot, BSTData data)
{
	BTreeNode * pNode = NULL;    // parent node
	BTreeNode * cNode = *pRoot;    // current node
	BTreeNode * nNode = NULL;    // new node

	// 새로운 노드가 추가될 위치를 찾는다.
	while(cNode != NULL)
	{
		if(data == GetData(cNode))
			return;    // 키의 중복을 허용하지 않음

		pNode = cNode;

		if(GetData(cNode) > data)
			cNode = GetLeftSubTree(cNode);
		else
			cNode = GetRightSubTree(cNode);
	}
	
	// pNode의 서브 노드에 추가할 새 노드의 생성
	nNode = MakeBTreeNode();    // 새 노드의 생성
	SetData(nNode, data);    // 새 노드에 데이터 저장

	// pNode의 서브 노드에 새 노드를 추가
	if(pNode != NULL)    // 새 노드가 루트 노드가 아니라면,
	{
		if(data < GetData(pNode))
			MakeLeftSubTree(pNode, nNode);
		else
			MakeRightSubTree(pNode, nNode);
	}
	else    // 새 노드가 루트 노드라면,
	{
		*pRoot = nNode;
	}
}

BTreeNode * BSTSearch(BTreeNode * bst, BSTData target)
{
	BTreeNode * cNode = bst;    // current node
	BSTData cd;    // current data

	while(cNode != NULL)
	{
		cd = GetData(cNode);

		if(target == cd)
			return cNode;
		else if(target < cd)
			cNode = GetLeftSubTree(cNode);
		else
			cNode = GetRightSubTree(cNode);
	}

	return NULL;
}

BTreeNode * BSTRemove(BTreeNode ** pRoot, BSTData target)
{
	// 삭제 대상이 루트 노드인 경우를 별도로 고려해야 한다.

	BTreeNode * pVRoot = MakeBTreeNode();    // 가상 루트 노드;

	BTreeNode * pNode = pVRoot;    // parent node
	BTreeNode * cNode = *pRoot;    // current node
	BTreeNode * dNode;    // delete node

	// 루트 노드를 pVRoot가 가리키는 노드의 오른쪽 서브 노드가 되게 한다.
	ChangeRightSubTree(pVRoot, *pRoot);
	
	// 삭제 대상을 저장한 노드 탐색
	while(cNode != NULL && GetData(cNode) != target)
	{
		pNode = cNode;

		if(target < GetData(cNode))
			cNode = GetLeftSubTree(cNode);
		else
			cNode = GetRightSubTree(cNode);
	}

	if(cNode == NULL)    // 삭제 대상이 존재하지 않는다면,
		return NULL;

	dNode = cNode;    // 삭제 대상을 dNode가 가리키게 한다.

	// 첫 번째 경우: 삭제할 노드가 단말 노드인 경우
	if(GetLeftSubTree(dNode) == NULL && GetRightSubTree(dNode) == NULL)
	{
		if(GetLeftSubTree(pNode) == dNode)
			RemoveLeftSubTree(pNode);
		else
			RemoveRightSubTree(pNode);
	}

	// 두 번째 경우: 하나의 자식 노드를 갖는 경우
	else if(GetLeftSubTree(dNode) == NULL || GetRightSubTree(dNode) == NULL)
	{
		BTreeNode * dcNode;    // delete node의 자식 노드

		if(GetLeftSubTree(dNode) != NULL)
			dcNode = GetLeftSubTree(dNode);
		else
			dcNode = GetRightSubTree(dNode);

		if(GetLeftSubTree(pNode) == dNode)
			ChangeLeftSubTree(pNode, dcNode);
		else
			ChangeRightSubTree(pNode, dcNode);
	}

	// 세 번째 경우: 두 개의 자식 노드를 모두 갖는 경우
	else
	{
		BTreeNode * mNode = GetRightSubTree(dNode);    // mininum node
		BTreeNode * mpNode = dNode;    // mininum node의 부모 노드
		int delData;

		// 삭제할 노드를 대체할 노드를 찾는다.
		while(GetLeftSubTree(mNode) != NULL)
		{
			mpNode = mNode;
			mNode = GetLeftSubTree(mNode);
		}
		
		// 대체할 노드에 저장된 값을 삭제할 노드에 대입한다.
		delData = GetData(dNode);    // 대입 전 데이터 백업
		SetData(dNode, GetData(mNode));

		// 대체할 노드의 부모 노드와 자식 노드를 연결한다.
		if(GetLeftSubTree(mpNode) == mNode)
			ChangeLeftSubTree(mpNode, GetRightSubTree(mNode));
		else
			ChangeRightSubTree(mpNode, GetRightSubTree(mNode));

		dNode = mNode;
		SetData(dNode, delData);    // 백업 데이터 복원
	}

	// 삭제된 노드가 루트 노드인 경우에 대한 처리
	if(GetRightSubTree(pVRoot) != *pRoot)
		*pRoot = GetRightSubTree(pVRoot);

	free(pVRoot);
	return dNode;
}

void ShowIntData(int data)
{
	printf("%d ", data);
}

void BSTShowAll(BTreeNode * bst)
{
	InorderTraverse(bst, ShowIntData);
}
```
</details>
<details>
<summary>BinarySearchTree.h</summary>

```C
#ifndef __BINARY_SEARCH_TREE2_H__
#define __BINARY_SEARCH_TREE2_H__

#include "BinaryTree3.h"

typedef BTData	BSTData;

// 이진 탐색 트리의 생성 및 초기화
void BSTMakeAndInit(BTreeNode ** pRoot);

// 노드에 저장된 데이터 반환
BSTData BSTGetNodeData(BTreeNode * bst);

// 이진 탐색 트리를 대상으로 데이터 저장(노드의 생성과정 포함)
void BSTInsert(BTreeNode ** pRoot, BSTData data);

// 이진 탐색 트리를 대상으로 데이터 탐색
BTreeNode * BSTSearch(BTreeNode * bst, BSTData target);

// 트리에서 노드를 제거하고 제거된 노드의 주소 값을 반환한다. 
BTreeNode * BSTRemove(BTreeNode ** pRoot, BSTData target);

// 이진 탐색 트리에 저장된 모든 노드의 데이터를 출력한다.
void BSTShowAll(BTreeNode * bst);

#endif
```
</details>
</details>

## 균형 잡힌 이진 탐색 트리: AVL 트리의 이해
이진 탐색 트리에 적용하여 트리의 불균형을 막는다.
### 균형 인수(Balance Factor)
균형 인수 = | 왼쪽 서브 트리의 높이 - 오른쪽 서브 트리의 높이 |
> 균형 인수가 | 2 | 보다 큰 노드가 있다면, 트리는 불균형한 상태다.
>> 리밸런싱이 필요한 상태!
<details>
<summary> AVL 트리 코드(출처: 윤성우의 열혈 자료구조)</summary>

<details>
<summary> AVLRebalance.c</summary>

```C
#include <stdio.h>
#include "BinaryTree3.h"

// LL 회전
BTreeNode * RotateLL(BTreeNode * bst)
{
	BTreeNode * pNode;
	BTreeNode * cNode;

	pNode = bst;
	cNode = GetLeftSubTree(pNode);

	ChangeLeftSubTree(pNode, GetRightSubTree(cNode));
	ChangeRightSubTree(cNode, pNode);
	return cNode;
}

// RR 회전
BTreeNode * RotateRR(BTreeNode * bst)
{
	BTreeNode * pNode;
	BTreeNode * cNode;

	pNode = bst;
	cNode = GetRightSubTree(pNode);

	ChangeRightSubTree(pNode, GetLeftSubTree(cNode));
	ChangeLeftSubTree(cNode, pNode);
	return cNode;
}

// RL 회전
BTreeNode * RotateRL(BTreeNode * bst)
{
	BTreeNode * pNode;
	BTreeNode * cNode;

	pNode = bst;
	cNode = GetRightSubTree(pNode);

	ChangeRightSubTree(pNode, RotateLL(cNode));   // 부분적 LL 회전
	return RotateRR(pNode);     // RR 회전
}

// LR 회전
BTreeNode * RotateLR(BTreeNode * bst)
{
	BTreeNode * pNode;
	BTreeNode * cNode;

	pNode = bst;
	cNode = GetLeftSubTree(pNode);
	
	ChangeLeftSubTree(pNode, RotateRR(cNode));   // 부분적 RR 회전
	return RotateLL(pNode);     // LL 회전
}

// 트리의 높이를 계산하여 반환
int GetHeight(BTreeNode * bst) 
{
	int leftH;		// left height
	int rightH;		// right height

	if(bst == NULL)
		return 0;

	// 왼쪽 서브 트리 높이 계산
	leftH = GetHeight(GetLeftSubTree(bst));

	// 오른쪽 서브 트리 높이 계산
	rightH = GetHeight(GetRightSubTree(bst));

	// 큰 값의 높이를 반환한다.
	if(leftH > rightH)
		return leftH + 1;
	else
		return rightH + 1;
}

// 두 서브 트리의 높이의 차를 반환
int GetHeightDiff(BTreeNode * bst)
{
	int lsh;    // left sub tree height
	int rsh;    // right sub tree height

	if(bst == NULL)
		return 0;

	lsh = GetHeight(GetLeftSubTree(bst));
	rsh = GetHeight(GetRightSubTree(bst));

	return lsh - rsh;
}

// 트리의 균형을 잡는다.
BTreeNode * Rebalance(BTreeNode ** pRoot)
{
	int hDiff = GetHeightDiff(*pRoot);

	if(hDiff > 1)     // 왼쪽 서브 트리 방향으로 높이가 2 이상 크다면
	{
		if(GetHeightDiff(GetLeftSubTree(*pRoot)) > 0)
			*pRoot = RotateLL(*pRoot);
		else
			*pRoot = RotateLR(*pRoot);
	}
	
	if(hDiff < -1)     // 오른쪽 서브 트리 방향으로 2 이상 크다면
	{
		if(GetHeightDiff(GetRightSubTree(*pRoot)) < 0)
			*pRoot = RotateRR(*pRoot);
		else
			*pRoot = RotateRL(*pRoot);
	}
	
	return *pRoot;
}
```
</details>
<details>
<summary>AVLRebalance.h</summary>>

```C
#ifndef __AVL_REBALANCE_H__
#define __AVL_REBALANCE_H__

#include "BinaryTree3.h"

// 트리의 균형을 잡는다.
BTreeNode * Rebalance(BTreeNode ** pRoot);

#endif
```
</details>
</details>
