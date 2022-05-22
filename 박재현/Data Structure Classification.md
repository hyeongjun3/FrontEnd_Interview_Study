# Data Structure Classification 자료구조 분류

데이터를 저장하고 접근, 수정할 수 있는 구조
- 각 용도에 맞는 가장 효율적인 구조가 있다
- 구성
  - 데이터 값들
  - 데이터 간의 관계
  - 데이터를 다루는 함수와 작업

## 대분류
### Primitive data structure 원시적 자료구조
- Integer
- Float
- Character
- Boolean

### Non-primitive data structure 비원시적 자료 구조
- Linear data structure 선형적 자료구조
  - Stack 스택
  - Queue 큐
  - Deque 딕/디큐
  - Linked List 연결 리스트
  - Array List 배열
- Non-linear data structure 비선형적 자료구조
  - Hash 해시
  - Tree 트리
    - Binary Tree 이진 트리
      - Heap 힙
  - Graph 그래프

## 소분류 (비원시적 자료 구조)
### Stack

### Queue

### Deque

### Linked List

### Array List
WIP: 
array with holes

### Hash

### Tree
Binary Tree
- Perfect Binary Tree
  - 모든 노드가 2개의 자식 노드를 갖고 + 모든 마지막 노드가 같은 레벨에 있다
- Full Binary Tree
  - 모든 노드가 2개의 자식 노드를 갖는다
- Complete Binary Tree
  - 마지막 레벨을 제외하고 모든 노드가 2개의 자식 노드를 갖고 + 마지막 레벨은 왼쪽부터 채워진다
- Degenerate Tree (Pathological Tree)
  - 모든 노드가 단 1개의 자식 노드를 갖는다
- Balanced Binary Tree
  - 좌우 서브트리 간 깊이 차이가 1이하

### Graph
WIP: 
bipartite graph



WIP:
## 알고리즘과의 관계
- BFS
  - Queue
- DFS
  - Stack
- Dijkstra's shortest path
  - account for
    - Graphs that are bipartite.
    - A graph with more than 256 nodes
    - 0 weighted nodes
  - doesnt account for
    - Negative edge weights


WIP: heap sort
The first step of Heap sort is to:
Remove the largest element from the heap
(this) Build a max/min heap
Convert the heap to a tree
Compress the heap