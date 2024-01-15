---
layout: single
title:  "자료구조, Sorted List"
categories: DataStructure
tag: [data_structure, c++]
toc: true
toc_sticky: true
author_profile: true
---

# 자료구조, Sorted List

## List
- Linear relationship
    - 리스트의 아이템들은 순서가 있음
    - 첫번째 요소를 제외한 모든 아이템이 unique predecessor (유일한 앞선 아이템)을 가짐
    - 모든 리스트는 길이를 가짐 (리스트 아이템의 갯수)
![list](https://media.geeksforgeeks.org/wp-content/uploads/20230404164103/ArrayList_Integer_in_Java.webp)
- Unsored List
    - 아이템들이 정렬되지 않은 리스트
- Sorted List
    - 아이템들이 정렬된 리스트

## List에 필요한 Operators
- Constructor
    - 리스트 객체를 생성하는데 필요한 생성자
- Transformer
    - 리스트의 상태 (들어있는 아이템의 값, 리스트의 길이 등)을 변경
- Observer
    - 리스트의 아이템이나 길이 등의 정보를 접근
- Iterator
    - 리스트의 모든 아이템을 원하는 상태로 변경
    - 리스트의 아이템에 순서대로 접근

## Sorted List(Array Based)
- 리스트의 아이템들이 정렬된 상태로 배열됨
- 아이템들이 정렬되어 있으면, 탐색이 빈번하게 발생하는 자료구조의 경우 용이하게 사용할 수 있음.
    - 탐색 알고리즘: 이진 탐색(Binary Search)
    - 삽입 Big-O: O(N)
    - 삭제 Big-O: O(N)
    - 탐색 Big-O: O(logN)

### 이진 탐색(Binary Search)
- 정렬된 리스트의 중간 값과 찾고자 하는 값을 비교하여 탐색

#### Precondition
- 정렬된 리스트

#### 이진 탐색의 동작 방식
1. 배열의 중간 값을 가져옴
2. 중간 값과 검색 값을 비교
    - 중간 값이 검색 값과 같으면 종료 (mid==key)
    - 중간 값보다 검색 값이 크면 기준 배열의 오른쪽 구간을 탐색 (mid<key)
    - 중간 값보다 검색 값이 작으면 기준 배열의 왼쪽 구간을 탐색 (mid>key)
3. 값을 찾거나 간격이 비어있을 때까지 반복

#### 검색 예

- 배열의 가운데를 결정

```cpp
mid = low + (high - low) / 2
```
<div style="text-align: center">$$4=0+(9-0)/2$$</div>
![step1](/images/2024-01-15-Sorted_List/step1.png)

- 중앙 값과 검색 값을 비교
    - A[4] < key 이므로 기존 구간의 오른쪽 구간이 탐색 범위

```cpp
low = mid + 1
```
<div style="text-align: center">$$5=4+1$$</div>
![step2](/images/2024-01-15-Sorted_List/step2.png)

- 중앙 값을 결정

```cpp
mid = low + (high - low) / 2
```
<div style="text-align: center">$$7=5+(9-5)/2$$</div>
![step3](/images/2024-01-15-Sorted_List/step3.png)

- 중앙 값과 검색 값을 비교
    - A[7] < key 이므로 기존 구간의 왼쪽 구간이 탐색 범위

```cpp
high = mid - 1
```
<div style="text-align: center">$$6=7-1$$</div>
![step4](/images/2024-01-15-Sorted_List/step4.png)

- 중앙 값을 결정

```cpp
mid = low + (high - low) / 2
```
<div style="text-align: center">$$5=5+(6-5)/2$$</div>
![step5](/images/2024-01-15-Sorted_List/step5.png)

- 중앙 값과 검색 값을 비교
    - A[5] = key 이므로 탐색을 종료

### Sorted List Operators(Array Based)
- Transformers
    - InsertItem
    - DeleteItem
    - MakeEmpty
- Observers
    - RetrieveItem
    - LengthIs
    - IsFull
- Iterators
    - ResetList
    - GetNextItem

## Source Code
- Dependency
    - OS: Windows11, 23H2
    - IDE: JetBrain CLion
    - Build Toolchain: CMake 3.27
        - Compiler: MSYS2(GCC for Windows)
        - C++ Standard: C++23

> In most cases, MSVC in Visual Studio is also available<br />
> GCC in UNIX and LINUX are always available<br />
> However, you must use a C++17 or higher STL

### Preprocessing
```cpp
#include <iostream>
#include <climits>
using namespace std;

#define MAX_ITEMS 50
typedef int ItemType;
class SortedType;
```
> typedef를 사용하는 이유는 후에 리스트 아이템의 자료형을 쉽게 변경하기 위함

> 하나의 소스파일에서 모든 멤버를 정의할 것이기 때문에 상단에 미리 클래스 및 함수 선언

### Class Definition
```cpp
class SortedType {
public:
    SortedType();
    [[nodiscard]] bool IsFull() const; //리스트가 가득 차있는지 확인
    [[nodiscard]] int LengthIs() const; //리스트 길이 반환
    bool RetrieveItem(ItemType& item); //리스트에 파라미터로 준 아이템이 있는지 확인
    void InsertItem(ItemType item); //입력받은 아이템을 삽입
    void DeleteItem(ItemType item); //입력받은 아이템과 일치하는 아이템을 삭제
    void MakeEmpty(); //현재 리스트의 모든 요소 삭제
    void ResetList(); //아이템을 가리키는 CurrentPos를 0으로 초기화
    ItemType GetNextItem(); //다음 위치의 아이템을 반환

private:
    int length;
    ItemType info[MAX_ITEMS]{};
    int currentPos;
};
```
> **Attribute에 대해서 알아보자**
> - C++11 이후로 추가된 기능
> - 함수 선언 시 attribute를 추가하여 컴파일러의 최적화에 도움을 줌
> - [[nodiscard, noreturn]]과 같은 방식으로 사용
> - 사용하지 않아도 상관 없음
> - C++11, C++14, C++17, C++20, C++23 버전별로 계속 추가됨

### Class Constructor
```cpp
SortedType::SortedType() {
    length = 0;
    currentPos = 0;
    for (int& i : info) {
        i = INT_MIN;
    }
}
```

### Class Transformer
```cpp
void SortedType::InsertItem(ItemType item) {
    //아이템을 삽입할 위치 찾기
    int correctPos = 0;
    for (int i = 0; i < length; i++) {
        if (info[i] <= item) {
            correctPos++;
        }
        else {
            break;
        }
    }
    //해당 위치 뒤부터 있는 아이템들을 한 칸씩 미루기
    for (int i = length; i > correctPos; i--) {
        info[i] = info[i-1];
    }
    //해당 위치에 넣고자 하는 아이템을 넣기
    info[correctPos] = item;
    length++; //길이를 1만큼 늘림
}
```

```cpp
void SortedType::DeleteItem(ItemType item) {
    //아이템을 삭제할 위치 찾기
    int correctPos = 0;
    for (int i = 0; i < length; i++) {
        if (info[i] != item) {
            correctPos++;
        }
        else {
            break;
        }
    }
    //해당 위치에 아이템을 한 칸 씩 앞으로 당기기
    for (int i = correctPos; i < length; i++) {
        info[i] = info[i+1];
    }
    info[length - 1] = INT_MIN;
    length--; //길이를 1만큼 줄임
}
```

```cpp
void SortedType::MakeEmpty() {
    for (int& i : info) {
        i = INT_MIN;
    }
    length = 0;
}
```

### Class Observer
```cpp
bool SortedType::RetrieveItem(ItemType& item) { //BinarySearch 구현
    int first = 0; //탐색 범위 시작 인덱스
    int last = length - 1; //탐색 범위 마지막 인덱스
    int midPoint = (first + last) / 2; //탐색 범위 중심 인덱스
    bool found = false; //탐색 완료 시 참으로 변경
    while ((first <= last) && !found) {
        midPoint = (first + last) / 2; //탐색 범위 중심 계산
        if (item < info[midPoint]) {
            last = midPoint - 1;
        }
        else if (item > info[midPoint]) {
            first = midPoint + 1;
        }
        else if (info[midPoint] == item) { //일치하는 아이템이 있으면
            found = true; //found 참으로 변환
        }
    }
    return found;
}
```

```cpp
int UnsortedType::LengthIs() const {
    return length;
}
```

```cpp
bool UnsortedType::IsFull() const {
    return length == MAX_ITEMS;
}
```

### Class Iterator
```cpp
void UnsortedType::ResetList() {
    currentPos = 0; //currentPos를 0으로 만들어 다시 처음부터 값 접근 가능
}
```

```cpp
ItemType UnsortedType::GetNextItem() {
    return info[currentPos++];
}
```

### Main Function
```cpp
int main() {
    SortedType list;

    list.InsertItem(30);
    list.InsertItem(10);
    list.InsertItem(20);
    list.InsertItem(70);
    list.InsertItem(60);

    cout << "Length of list: " << list.LengthIs() << endl;

    cout << "Items in the list (sorted): ";
    list.ResetList();
    for (int i = 0; i < list.LengthIs(); i++) {
        cout << list.GetNextItem() << " ";
    }
    cout << endl;

    if (list.IsFull())
        cout << "The list is full." << endl;
    else
        cout << "The list is not full." << endl;

    int item = 20;
    list.ResetList();
    if (list.RetrieveItem(item))
        cout << "Item " << item << " found in the list." << endl;
    else
        cout << "Item " << item << " not found in the list." << endl;

    list.DeleteItem(20);
    cout << "Item 20 deleted." << endl;
    cout << "Length of list after deletion: " << list.LengthIs() << endl;

    cout << "Items in the list (sorted): ";
    list.ResetList();
    for (int i = 0; i < list.LengthIs(); i++) {
        cout << list.GetNextItem() << " ";
    }
    cout << endl;

    return EXIT_SUCCESS;
}
```

#### 참고문헌
- Nell Dale. (2016). "C++ Plus Data Structues Sizth Edition". Jones&Bartlett Learning.
- GeeksforGeeks. (2024). "ArrayList in Java". [https://www.geeksforgeeks.org/arraylist-in-java/](https://www.geeksforgeeks.org/arraylist-in-java/)
- OpenAI. (2024). ChatGPT(Jan 10, 2024). GPT-4. [https://chat.openai.com](https://chat.openai.com)
- meongju0o0. (2024). "sorted_list.cpp". [https://github.com/meongju0o0/meongju0o0-data-structure](https://github.com/meongju0o0/meongju0o0-data-structure)