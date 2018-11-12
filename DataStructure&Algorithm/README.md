

### Data Structure & Algorithm

---

- [Graph](#graph)
- [Soritng Algorithm](#sorting-algorithm)
- [Search Algorithm](#search-algorithm)

---



### Graph

---

**Adjacency matrix vs Adjacency list**

|                | Adjacency matrix | Adjacency List |
| -------------- | ---------------- | -------------- |
| Complete graph | O(n^2)           | O(n^2)         |
| Sparse graph   | O(n^2)           | O(n)           |



#### DFS

- 노드 v를 방문한다 - visted[v]=true
- v와 인접한 방문하지 않은 node w를 Stack에 삽입한다.
- 해당 노드의 키가 답이 아니면 pop한 node 부터 다시 DFS를 수행한다.
- Recursive call
- Connected Component, Spanning tree, Biconnected Components



*ex) Recursive Call 을 이용한 완전 탐색시*

- *조건을 만족하지 못하고 종료하는 조건*
- *조건을 만족하고 카운트 또는 출력하는 조건*
- *다음 경우의 수 (재귀호출)*



#### BFS

- 모든 인접한 vertex을 visited check하고 Queue에 삽입한다. 
- deleteQueue한 노드부터 다시 BFS를 수행한다
- Queue가 empty일 때까지 반복한다.
- Connected Component, Shortest Path
- 가중치 없는 그래프의 최단경로/ 가중치가 0또는 1(DEQ)





### Minimum Spanning Tree

그래프 G 의 spanning tree 중 edge weight 의 합이 최소인 `spanning tree`를 말한다. 여기서 말하는 `spanning tree`란 그래프 G 의 모든 vertex 가 cycle 이 없이 연결된 형태를 말한다.

### Kruskal Algorithm

초기화 작업으로 **edge 없이** vertex 들만으로 그래프를 구성한다. 그리고 weight 가 제일 작은 edge 부터 검토한다. 그러기 위해선 Edge Set 을 non-decreasing 으로 sorting 해야 한다. 그리고 가장 작은 weight 에 해당하는 edge 를 추가하는데 추가할 때 그래프에 cycle 이 생기지 않는 경우에만 추가한다. spanning tree 가 완성되면 모든 vertex 들이 연결된 상태로 종료가 되고 완성될 수 없는 그래프에 대해서는 모든 edge 에 대해 판단이 이루어지면 종료된다.

#### 어떻게 cycle 생성 여부를 판단하는가?

Graph 의 각 vertex 에 `set-id`라는 것을 추가적으로 부여한다. 그리고 초기화 과정에서 모두 1~n 까지의 값으로 각각의 vertex 들을 초기화 한다. 여기서 0 은 어떠한 edge 와도 연결되지 않았음을 의미하게 된다. 그리고 연결할 때마다 `set-id`를 하나로 통일시키는데, 값이 동일한 `set-id` 개수가 많은 `set-id` 값으로 통일시킨다.

#### Time Complexity

1. Edge 의 weight 를 기준으로 sorting - O(E log E)
2. cycle 생성 여부를 검사하고 set-id 를 통일 - O(E + V log V) => 전체 시간 복잡도 : O(E log E)

### Prim Algorithm

초기화 과정에서 한 개의 vertex 로 이루어진 초기 그래프 A 를 구성한다. 그리고나서 그래프 A 내부에 있는 vertex 로부터 외부에 있는 vertex 사이의 edge 를 연결하는데 그 중 가장 작은 weight 의 edge 를 통해 연결되는 vertex 를 추가한다. 어떤 vertex 건 간에 상관없이 edge 의 weight 를 기준으로 연결하는 것이다. 이렇게 연결된 vertex 는 그래프 A 에 포함된다. 위 과정을 반복하고 모든 vertex 들이 연결되면 종료한다.

#### Time Complexity

=> 전체 시간 복잡도 : O(E log V)



### Sorting Algorithm

---

#### Bubble Sort

인접한 두 원소를 비교하여 가장 큰 원소가 맨 뒤에 오도록 정렬하는 방법이다. 가장 간단하나 효율이 낮기 때문에 잘 쓰이지 않는다. 

**순서**

- 첫 번째 원소부터 바로 옆의 원소와 비교하며 교환한다.
- 한 회전이 지나면 최댓값이 맨 마지막에 위치해 있다.
- 첫 번째 원소부터 마지막-1 원소까지 반복한다.

**Ex**

`29` `10` `14` `37` `13`  -> 1-5까지 인접한 원소와 비교하며 교환

`10` `14` `29` `13` `37` -> 37이 맨 뒤에 온 상태, 1- 4까지 인접한 원소와 비교하며 교환 

`10` `14` `13` `29` `37` -> 29가 정렬된 상태, 1-3까지 인접한 원소와 비교하며 교환 

`10` `13` `14` `29` `37` -> 14가 정렬된 상태, 1-2까지 인접한 원소와 비교하며 교환

`10` `13` `14` `29` `37` -> 유지, 정렬 완료 



#### Selection Sort

해당 순서에 원소를 넣을 위치는 정해져 있고, 어떤 원소를 넣을지 선택하는 알고리즘으로 하나의 원소가 남을 때까지 반복한다. 

**순서**

- 마지막 원소부터 넣을 원소를 찾는다.
- 첫 번째 원소부터 마지막 원소까지 탐색하고 가장 큰 원소와 마지막 원소를 교환한다.

- 마지막에서 두 번째 원소를 선택한다.
- 원소가 하나 남을 때까지 반복한다.

**Ex**

`29` `10` `14` `37` `13` -> 가장 큰 원소를 찾음 

`29` `10` `14` `13` `37` -> 37과 13 교환, 1-4 중 가장 큰 원소 찾음 

`13` `10` `14` `29` `37` -> 29와 13 교환, 1-3 중 가장 큰 원소 찾음 

`13` `10` `14` `29` `37` -> 유지, 1-2 중 가장 큰 원소 찾음

`10` `13` `14` `29` `37` -> 13과 10 교환, 정렬 완료



#### Insertion Sort

매 순서마다 해당 원소를 삽입할 수 있는 위치를 찾아 삽입하는 방법.

**순서**

- 두 번째 원소부터 시작한다.
- 첫 번째 원소부터 현재 순서 전까지의 원소는 정렬된 상태이다.
- 두 번째 원소가 삽입될 위치를 찾고 삽입한다.
- 마지막 원소까지 반복한다.

**Ex**

`15` `12` `13` `10` `14` `11` -> 두 번째 원소가 삽입 될 위치를 1-1까지 중 탐색

`12` `15` `13` `10` `14` `11` -> 15의 앞에 12 삽입. 세 번째 원소(13) 가 삽입될 위치를 1-2까지 중 탐색 

`12` `13` `15` `10` `14` `11` -> 두 번째 자리에 13 삽입. 네 번째 원소(10)가 삽입될 위치를 1-3까지 중 탐색

`10` `12` `13` `15` `14` `11` -> 맨 앞에 삽입. 다섯 번째 원소(14)가 삽입될 위치를 1-4까지 중 탐색 

`10` `12` `13` `14` `15` `11` -> 15의 위치에 삽입. 여섯 번째 원소(11)가 삽입될 위치를 1-5까지 중 탐색.

`10` `11` `12` `13` `14` `15` -> 12의 앞에 삽입. 정렬 끝

#### Merge Sort

더이상 나누어지지 않을 때까지 반 씩 분할하다가, 더 이상 나누어지지 않는 경우에 자신의 원소 하나를 반환한다. 반환된 원소끼리 combine하면서 비교가 이루어진다. **정렬은 병합하는 과정**에서 이루어진다. 

***cf) External Merge sort***

대상 데이터가 테이프나 디스크에 저장되어있고 데이터가 너무 커서 메모리에 담을 수 없는 경우 실용적인 방법.



#### Heap Sort

데이터들을 힙에 넣었다가 꺼내는 원리로 sorting하거나 기존 배열을 hepify한 후 꺼내는 원라로 sorting한다. 

**Heap**

- 우선 순위에 자주 접근하거나 우선순위 중심으로 정렬된 시퀀스를 활용해야할 때 유용한 자료구조로 완전이진트리 기반이다. 

**Heap vs Binary Search Tree**

- 공통점: 이진트리

- 차이점: 힙은 각 노드의 값이 자식 노드의 값보다 큰 반면, BST는 왼쪽 자식노드가 제일 작고 부모노드가 그 다음으로 크며, 오른쪽 자식노드가 가장 큰 값을 가진다. 힙은 우선순위 정렬에, BST는 탐색에 강점을 지닌다. 


#### Quick Sort

임의의 pivot을 정하고 pivot을 기준으로 좌측은 pivot보다 작은 값, 우측은 큰 값이 위치하도록 partition한다. 그리고 pivot을 기준으로 두 배열을 재귀적으로 다시 partition을 반복한다. 이때, pivot으로 설정된 값은 다음 재귀과정에 포함시키지 않는다. 이미 **정렬된 상태의 배열을 Quick sort로 정렬할 경우 O(n^2)의 시간복잡도**를 가진다. 



#### Counting Sort

배열의 값의 범위를 알고 있을 때 사용할 수 있는 Sorting 알고리즘이다. 정렬하고자 하는 값 중 최대값에 해당하는 값을 size로 하는 임시 배열을 만든다. 그리고 해당 index의 value는 index값을 가진 원래 배열의 원소의 개수를 나타낸다. 이때, 누적 합으로 바꾸어주는 과정이 필요하다. 배열의 값이 0 1 ~ ... (비어있음) _ 100000 과 같이 범위가 정해져 있지만 넓을 경우 공간을 낭비하게 됨으로 한계가 있다.



#### Radix Sort

자리수를 비교해서 정렬하는 방식으로 대부분의 문자열, 정수에 적용 가능하다. 가장 작은 자리수를 비교하는 LSD 방식과 가장 큰 자리수부터 비교하는 MSD 방식으로 나눌 수 있다. 

**LSD**

17, 298, 31, 9, 79, 3, 500 

***1의 자리수 기준 정렬***

`500` `31` `3` `17` `298` `9, 79`

***10의 자리수 기준 정렬***

`500, 3, 9` `17` `31`  `79` `298`

***100의 자리수 기준 정렬***

`3, 9, 17, 31, 79` `298` `5000` 

<< 정렬 완료 >>



#### Sorting Algorithm's Complexity 정리

| Algorithm      | Space Complexity | (average) Time Complexity | (worst) Time Complexity | (Best) Time Complexity |
| -------------- | ---------------- | ------------------------- | ----------------------- | ---------------------- |
| Bubble sort    | O(1)             | O(n^2)                    | O(n^2)                  | O(n^2)                 |
| Selection sort | O(1)             | O(n^2)                    | O(n^2)                  | O(n^2)                 |
| Insertion sort | O(1)             | O(n^2)                    | O(n^2)                  | O(n)                   |
| Merge sort     | O(n)             | O(nlogn)                  | O(nlogn)                | O(nlogn)               |
| Heap sort      | O(1)             | O(nlogn)                  | O(nlogn)                | O(nlogn)               |
| Quick sort     | O(1)             | O(nlogn)                  | O(n^2)                  | O(nlogn)               |
| Counting sort  | O(n)             | O(n)                      | O(n)                    | O(n)                   |
| Radix sort     | O(n)             | O(n)                      | O(n)                    | O(n)                   |



***cf) 안정정렬 vs 비안정정렬***

- 안정정렬: 동일한 값에 대해 기존의 순서가 유지되는 정렬 방식 

| Algorithm      | 안정성 | 추가메모리 |
| -------------- | ------ | ---------- |
| Bubble sort    | O      | X          |
| Selection sort | X      | X          |
| Insertion sort | O      | X          |
| Merge sort     | O      | O          |
| Heap sort      | X      | X          |
| Quick sort     | X      | X          |
| Counting sort  | O      | O          |
| Radix sort     | O      | O          |







### Search Algorithm

---

#### Binary Search Tree

- 각 노드는 하나의 키 값만 가질 것
- 왼쪽 서브트리의 값은 루트노드의 값보다 항상 작을 것
- 오른쪽 서브트리의 값은 루트노드의 값보다 항상 클 것
- 왼쪽 서브트리와 오른쪽 서브트리도 Binary Search Tree일 것



**Best case**

Balanced Tree 일 경우 O(log n)으로 탐색 가능하다

**Worst Case**

Skewed Tree 일 경우 O(n) 으로 탐색된다. 





#### Heap (= Priority Queue)

트리 기반의 우선순위 큐 

- Complete Binary Tree

- Max  Heap: 모든 노드의 키가 자식 노드의 키보다 큰 값을 가지는 것
- Min  Heap: 모든 노드의 키가 자식 노드의 키보다 작은 값을 가지는 것
- Topdown heapify/ Bottomup heapify

| Data Structure     | Insert   | Delete   | Search   | Get max  |
| ------------------ | -------- | -------- | -------- | -------- |
| Unsorted Array     | O(1)     | O(n)     | O(n)     | O(n)     |
| Sorted Array       | O(n)     | O(n)     | O(log n) | O(1)     |
| Binary Search Tree | O(log n) | O(log n) | O(log n) | O(log n) |
| Heap               | O(log n) | O(log n) | O(n)     | O(1)     |
| Hash Table         | O(1)     | O(1)     | O(1)     | O(1)     |



#### Red Black Tree

---

   BST를 기반으로하는 트리형식의 자료구조로 Search, Insert, Delete에 O(log n) 시간 소요.

"동일한 노드의 개수일 때, depth를 최소화하여 시간 복잡도를 줄이는 것이 핵심 아이디어(=complete binary tree)"



#### HashTable

---

  내부적으로 배열을 사용하여 데이터를 저장하기 때문에 빠른 검색 속도를 가짐. Search하는 데 데이터 고유의 인덱스로 접근하기 때문에 average case에 대해 O(1)을 가진다. 

- 저장되는 값들의 key 값을 hash function을 통해 작은 범위의 고유한 인덱스 값으로 변경한다. 



### Greedy Algorithm

---

  각 단계에서 가장 최선의 선택을 하는 알고리즘으로 동적계획법보다 수행 시간이 빠르다. 현재의 선택이 다음 선택에 전혀 무관한 값이어야 하며, 매 순간의 최적해가 문제에 대한 최적해일 때 적용 가능하다. 또는, 최적해 대신 근사해를 구할 때 사용할 수도 있다.



### DP

---

  복잡한 문제를 간단한 여러 개의 하위 문제로 나누어 푸는 방법으로 중간 계산 과정을 저장해두었다가 사용하여 효율을 높인다는 특징이 있다. Top-down 방식과 Bottom-up 방식이 있다. 