# 테이블(Table)과 해쉬(Hash)

## 테이블 자료구조의 이해
> 현실에서의 테이블 사례 -> 사전, 우편함 <br/> 사전 -> '단어' = key, '설명, 내용' = value
<br/> 우편함 -> '호수' = key, '우편물' = value.
- 데이터가 key와 value로 한 쌍을 이룬다.
- key가 데이터의 저장 및 탐색의 도구가 된다.
- 테이블은 사전 구조 또는 맵(map)이라고도 불린다.
- 배열을 기반으로 만들어야 단번에 탐색 가능하다.

## 해쉬 함수(Hash fuction)
### 넓은 범위의 키를 좁은 범위의 키로 변경하는 역할을 한다.
> 좋은 해쉬 함수는 키의 일부분을 참조하여 해쉬 값을 만들지 않고, 키 전체를 참조하여 해쉬 값을 만들어 낸다.

- 자릿수 선택(Digit Selection) 방법과 자릿수 폴딩(DIgit Folding) 방법 등 다양한 방법이 있다.

<details>
<summary>해쉬 함수 코드(출처: 윤성우의 열혈 자료구조)</summary>

```C
#include <stdio.h>

typedef struct _empInfo
{
	int empNum;    // 직원의 고유번호
	int age;     // 직원의 나이
}EmpInfo;

int GetHashValue(int empNum)
{
	return empNum % 100;
}

int main(void)
{
	EmpInfo empInfoArr[100];

	EmpInfo emp1={20120003, 42};
	EmpInfo emp2={20130012, 33};
	EmpInfo emp3={20170049, 27};

	EmpInfo r1, r2, r3;

	// 키를 인덱스 값으로 이용해서 저장
	empInfoArr[GetHashValue(emp1.empNum)] = emp1;
	empInfoArr[GetHashValue(emp2.empNum)] = emp2;
	empInfoArr[GetHashValue(emp3.empNum)] = emp3;

	// 키를 인덱스 값으로 이용해서 탐색
	r1 = empInfoArr[GetHashValue(20120003)];
	r2 = empInfoArr[GetHashValue(20130012)];
	r3 = empInfoArr[GetHashValue(20170049)];

	// 탐색 결과 확인
	printf("사번 %d, 나이 %d \n", r1.empNum, r1.age);
	printf("사번 %d, 나이 %d \n", r2.empNum, r2.age);
	printf("사번 %d, 나이 %d \n", r3.empNum, r3.age);
	return 0;
}
```
</details>

## 충돌(Collsision) 문제의 해결책

### - 선형 조사법(Linear Probing)
> 충돌이 발생했을 때 그 옆자리가 비었는지 살펴보고, 비었을 경우 그 자리에 대산 저장하는 방식 <br/> 
f(k) + 1 -> f(k) + 2 -> f(k) + 3 (f(k)는 해쉬 함수)
- 선형 조사법은 단순하지만, 충돌의 횟수가 증가함에 따라서 클러스터 현상(특정 영역에 데이터가 몰리는 현상)이 발생한다는 단점이 있다.
- 단점 때문에 사용되지 않음.

### - 이차 조사법
> f(k) + 1^2 -> f(k) + 2^2 -> f(k) + 3^2 (f(k)는 해쉬 함수)
- 선형 조사법 보다는 괜찮은 방식이나, 궁극적으로 클러스터 현상을 해결할 수 있는 방식은 아님.

### - 이중 해쉬의 이해
> 해쉬 값이 같으면, 충돌 발생시 빈 슬롯을 찾기 위한 접근 위치가 늘 동일하다는 문제점을 해결한 방법으로 총 두 개의 해쉬 함수를 활용하는 방법이다.
- 1차 해쉬 함수 - 키를 근거로 저장위치를 결정하기 위한 것
- 2차 해쉬 함수 - 충돌 발생시 몇 칸 뒤를 살필지 결정하기 위한 것
### 이상적인 충돌 해결책으로 알려져있다.

### 체이닝(닫힌 어드레싱 모델)
> 앞서 소개한 유형의 방법들을 가리켜 '열린 어드레싱 방법이라고 하는데, 이는 충돌이 발생하면 다른 자리에 대신 저장한다는 의미였다. 이 방법은 무슨 일이 있어도 자신의 자리에 저장한다는 의미다.
- 한 해쉬 값에 다수의 데이터를 저장할 수 있도록 배열을 2차원의 형태로 선언하는 모델(잘 거론되지 않음)
- 한 해쉬 값에 다수의 데이터를 저장할 수 있도록 각 해쉬 값 별로 연결 리스트를 구성하는 모델(대표 방법)

<details>
<summary>체이닝 테이블 코드(출처: 윤성우의 열혈 자료구조)</summary>

- 연결 리스트는 이전 챕터의 구현 소스를 활용
<details>
<summary>Tablc2.c(★)</summary>

```C
#include <stdio.h>
#include <stdlib.h>
#include "Table2.h"
#include "DLinkedList.h"	// 연결 리스트 구현 헤더파일

// int MyHashFunc(int k)
// {
// 	return k % 100;
// }
// 해쉬 함수 구현 예시

void TBLInit(Table * pt, HashFunc * f)	// HasgFunc은 직접 정의
{
	int i;

	for(i=0; i<MAX_TBL; i++)	// 연결 리스트 초기화
		ListInit(&(pt->tbl[i]));

	pt->hf = f;
}

void TBLInsert(Table * pt, Key k, Value v)
{
	int hv = pt->hf(k);	// 해쉬 함수를 통해 해쉬 값 얻기
	Slot ns = {k, v};
	
	if(TBLSearch(pt, k) != NULL)       // 키가 중복되었다면
	{
		printf("키 중복 오류 발생 \n");
		return;
	}
	else
	{
		LInsert(&(pt->tbl[hv]), ns);	// 해당 해쉬 값 연결 리스트에 삽입
	}
}

Value TBLDelete(Table * pt, Key k)
{
	int hv = pt->hf(k);
	Slot cSlot;

	if(LFirst(&(pt->tbl[hv]), &cSlot))
	{
		if(cSlot.key == k)
		{
			LRemove(&(pt->tbl[hv]));
			return cSlot.val;
		}
		else
		{
			while(LNext(&(pt->tbl[hv]), &cSlot))
			{
				if(cSlot.key == k)
				{
					LRemove(&(pt->tbl[hv]));
					return cSlot.val;
				}
			}
		}
	}

	return NULL;
}

Value TBLSearch(Table * pt, Key k)
{
	int hv = pt->hf(k);
	Slot cSlot;

	if(LFirst(&(pt->tbl[hv]), &cSlot))
	{
		if(cSlot.key == k)
		{
			return cSlot.val;
		}
		else
		{
			while(LNext(&(pt->tbl[hv]), &cSlot))
			{
				if(cSlot.key == k)
					return cSlot.val;
			}
		}
	}

	return NULL;
}
```
</details>
<details>
<summary>Table2.h(★)</summary>

```C
#ifndef __TABLE2_H__
#define __TABLE2_H__

#include "Slot2.h"
#include "DLinkedList.h"    // 이전 챕터 연결 리스트 활용

#define MAX_TBL     100
// 해쉬 함수 함수 포인터 선언
typedef int HashFunc(Key k);    

typedef struct _table
{
//	Slot tbl[MAX_TBL];
	List tbl[MAX_TBL];
	HashFunc * hf;
} Table;

void TBLInit(Table * pt, HashFunc * f); 
void TBLInsert(Table * pt, Key k, Value v);
Value TBLDelete(Table * pt, Key k);
Value TBLSearch(Table * pt, Key k);

#endif
```
</details>

<details>
<summary>Slot.h</summary>

```C
#ifndef __SLOT2_H__
#define __SLOT2_H__

#include "Person.h"

typedef int Key;
typedef Person * Value;

typedef struct _slot
{
	Key key;
	Value val;
} Slot;

#endif
```
</details>
<details>
<summary>Person.c</summary>

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "Person.h"

int GetSSN(Person * p)
{
	return p->ssn;
}

void ShowPerInfo(Person * p)
{
	printf("주민등록번호: %d \n", p->ssn);
	printf("이름: %s \n", p->name);
	printf("주소: %s \n\n", p->addr);
}

Person * MakePersonData(int ssn, char * name, char * addr)
{
	Person * newP = (Person*)malloc(sizeof(Person));

	newP->ssn = ssn;
	strcpy(newP->name, name);
	strcpy(newP->addr, addr);
	return newP;
}

```
</details>

<details>
<summary>Person.h</summary>

```C
#ifndef __PERSON_H__
#define __PERSON_H__

#define STR_LEN    50

typedef struct _person
{
	int ssn;				// 주민등록번호(key)
	char name[STR_LEN];		// 이름
	char addr[STR_LEN];		// 주소
} Person;

int GetSSN(Person * p);
void ShowPerInfo(Person * p);
Person * MakePersonData(int ssn, char * name, char * addr);

#endif
```
</details>
<details>
<summary>main.c</summary>

```C
#include <stdio.h>
#include <stdlib.h>
#include "Person.h"
#include "Table2.h"

int MyHashFunc(int k)
{
	return k % 100;
}

int main(void)
{
	Table myTbl;
	Person * np;
	Person * sp;
	Person * rp;

	TBLInit(&myTbl, MyHashFunc);

	// 데이터 입력 ///////
	np = MakePersonData(900254, "Lee", "Seoul");
	TBLInsert(&myTbl, GetSSN(np), np);

	np = MakePersonData(900139, "KIM", "Jeju");
	TBLInsert(&myTbl, GetSSN(np), np);

	np = MakePersonData(900827, "HAN", "Kangwon");
	TBLInsert(&myTbl, GetSSN(np), np);

	// 데이터 탐색 ///////
	sp = TBLSearch(&myTbl, 900254);
	if(sp != NULL)
		ShowPerInfo(sp);

	sp = TBLSearch(&myTbl, 900139);
	if(sp != NULL)
		ShowPerInfo(sp);

	sp = TBLSearch(&myTbl, 900827);
	if(sp != NULL)
		ShowPerInfo(sp);

	// 데이터 삭제 ///////
	rp = TBLDelete(&myTbl, 900254);
	if(rp != NULL)
		free(rp);

	rp = TBLDelete(&myTbl, 900139);
	if(rp != NULL)
		free(rp);

	rp = TBLDelete(&myTbl, 900827);
	if(rp != NULL)
		free(rp);

	return 0;
}
```
</details>

</details>