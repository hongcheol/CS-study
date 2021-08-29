# 알고리즘
1. 알고리즘 분석
	1. 시간 복잡도와 공간복잡도
	2. 알고리즘의 정당성 증명
2. 알고리즘 설계 패러다임
	1. 완전 탐색
	2. 분할 정복
	3. 동적 계획법
	4. 탐욕법
	5. 조합 탐색
	6. 최적화 문제 결정문제로 바꿔풀기
3. 유명한 알고리즘
	1. 수치해석
	2. 정수론
		1. 소수
		2. 유클리드 알고리즘
		3. 모듈라 연산
	3. 계산 기하
4. 기초 자료구조
	1. 비트마스크
	2. 부분 합
	3. 선형 자료 구조
	4. 큐와 스택, 데크
	5. 문자열
5. 트리
	1. 트리의 구현과 순회
	2. [이진 탐색트리](#이진-탐색트리)
	3. [우선순위 큐와 힙](#우선순위-큐와-힙)
	4. 구간 트리
	5. 상호 배타적 집합
	6. 트라이
6. [그래프](#그래프)
   1. [그래프의 표현과 정의](#그래프의-표현과-정의)
   2. [DFS](#DFS)
   3. BFS
   4. 최단 경로 알고리즘
      1. 다익스트라
      2. 벨만-포드
      3. 플로이드의 모든 쌍 최단 거리 알고리즘
   5. 최소 스패닝 트리
      1. 크루스칼의 최소 스패닝 트리 알고리즘
      2. 프림의 최소 스패닝 트리 알고리즘
   6. 네트워크 유량
      1. 포드-풀커슨 알고리즘
      2. 네트워크 모델링
      3. 이분 매칭
7. 정렬



# 트리



# 이진 탐색트리

이진 탐색 트리(BST, Binary Search Tree)는 이진 트리 기반의 탐색을 위해 특정한 조건들로 이루어진 이진 트리입니다.

<p align="center"><img src="./img/BST.png" width="400"></p>



1. 모든 노드의 키(Key)는 유일하다

   - Key는 노드에 기록한 데이터 값을 말합니다. 중복된 데이터 값을 갖는 노드가 없어야 합니다.

2. 왼쪽 서브 트리의 키들은 루트의 키보다 작다.

   - 루트 노드의 데이터가 10이라면, 왼쪽 서브트리에는 10보다 작은 값들만 존재해야 합니다.

3. 오른쪽 서브 트리의 키들은 루트의 키보다 크다.

   - 마찬가지로 오른쪽 서브트리에는 10보다 큰 값들만 존재해야 합니다.

4. 왼쪽과 오른쪽 서브 트리도 이진 탐색 트리이다.

   - 1~3의 조건들이 각 서브트리에 순환적으로 적용되어야 합니다.

   

이진 탐색 트리는 이진 암호화, 파일 시스템에 주로 쓰입니다.



## 이진 탐색트리 구현

### 검색

이진 탐색트리에서 60을 찾는 과정은 다음과 같습니다.

1. 루트부터 탐색을 시작합니다.
 	2. 목표 값과 현재 루트의 데이터를 비교합니다. 목표 값 < 현재 값이면 왼쪽, 반대면 오른쪽으로 이동합니다.
 	3. 일치하는 값을 찾으면 탐색을 멈춥니다.
 	4. 만약 목표 값이 없다면 null을 리턴합니다.

<p align="center"><img src="./img/BST_search.png" width="600"></p>

```java
public Node findNode(int key) {
    // 트리가 비었을 때
    if (root == null) return null;

    Node focusNode = root;

    while (focusNode.key != key) {
        if (key < focusNode.key) {              // 현재노드보다 작으면
            focusNode = focusNode.leftChild;    // 왼쪽으로
        } else {                                // 크면
            focusNode = focusNode.rightChild;   // 오른쪽으로
        }

        // 찾으려는 노드가 없을 때
        if (focusNode == null)
            return null;
    }

    return focusNode;
}
```



### 삽입

이진 탐색트리에 값을 삽입할 때 삽입할 위치를 찾는 과정은 값을 검색하는 과정과 유사하게 진행됩니다. 10을 삽입하는 과정은 다음과 같습니다.

1. 루트에서 시작합니다.
2. 삽입 값을 현재 루트의 값과 비교합니다.  삽입 값 < 현재 값이면 왼쪽, 반대면 오른쪽으로 이동합니다.
3. null을 만나면 그 이전 루트에 연결하여 값을 삽입합니다.

값을 검색하는 과정에서 null값을 만났을 때 null을 리턴 했다면, 삽입할 때는 그 곳에 값을 삽입하게됩니다.

<p align="center"><img src="./img/BST_insert.png" width="600"></p>

```java
public void addNode(int key) {
    if (findNode(key) != null) return;  // 이미 존재하면 그냥 리턴

    Node newNode = new Node(key);

    if (root == null) {
        root = newNode; // 트리가 비어있으면 root 에 삽입
    } else {
        Node focusNode = root;  //  탐색용 노드
        Node parent;            //  탐색용 노드의 부모 노드

        while(true) {
            parent = focusNode; //  이동

            if (key < parent.key) {             //  삽입하려는 키가 현재 노드보다 작으면
                focusNode = parent.leftChild;   //  왼쪽으로 이동

                if (focusNode == null) {        //  왼쪽 노드가 비어있으면
                    parent.leftChild = newNode; //  왼쪽 노드에 삽입
                    return;
                }
            } else {                            //  삽입하려는 키가 현재 노드와 같거나 크다면
                focusNode = parent.rightChild;  //  오른쪽으로 이동

                if (focusNode == null) {        //  오른쪽 노드가 비어있으면
                    parent.rightChild = newNode;//  오른쪽 노드에 삽입
                    return;
                }
            }
        }
    }
}
```



### 삭제

이진 탐색 트리에서 값을 삭제할 때에도 삭제할 값을 검색하는 과정을 거칩니다. 그리고 삭제할 값을 찾았을 때 3가지 경우를 고려해야 합니다.

1. 삭제할 노드가 leaf 노드인 경우

   이 경우에는 해당 노드를 삭제하면 됩니다.

   <p align="center"><img src="./img/BST_delete1.png" width="600"></p>


2. 삭제할 노드에 자식이 하나만 있는 경우

   이 경우에는 삭제할 노드의 자식 노드를 삭제할 노드의 부모 노드에 연결한 뒤 삭제하면 됩니다.

<p align="center"><img src="./img/BST_delete2.png" width="600"></p>


3. 삭제할 노드에 자식이 둘 있는 경우

   이 경우에는 이전보다 다소 복잡해집니다. 이 때는 삭제할 노드의 왼쪽 서브 트리에 있는 값 중 가장 큰 값, 또는 오른쪽 서브 트리에 있는 값 중 가장 작은 값 중 하나를 삭제할 노드의 부모 노드에 연결해야 합니다.

<p align="center"><img src="./img/BST_delete3.png" width="600"></p>

​		이렇게 하는 이유는 이진 탐색 트리의 규칙을 지킬 수 있는 최선의 방법이기 때문입니다.

​		루트의 왼쪽 서브트리의 가장 오른쪽 값은 루트보다 작은 가장 가까운 수이며, 오른쪽 서브트리		의 가장 왼쪽 값은 루트보다 큰 가장 가까운 수입니다.

<p align="center"><img src="./img/BST_delete3-2.png" width="600"></p>

​		50을 삭제하는 경우를 봅시다. 60은 루트 노드의 오른쪽 서브 트리의 가장 왼쪽 값입니다. 이 값		을 루트의 자리에 놓고 50을 60이 있던 자리에 넣은 뒤 자식이 없는 노드를 삭제할 때 처럼 삭제		하면 됩니다.

``` java
public boolean deleteNode(int key) {
    // focusNode 와 parent 가 같을 수 있는 경우는 찾으려는 key 가 root 인 경우
    Node focusNode = root;
    Node parent = root;

    boolean isLeftChild = true;

    // while 문이 끝나고 나면 focusNode 는 삭제될 노드를 가리키고, parent 는 삭제될 노드의 부모노드를 가리키게 되고, 삭제될 노드가 부모노드의 left 인지 right 인지에 대한 정보를 가지게 된다
    while(focusNode.key != key) {
        parent = focusNode; // 삭제할 노드를 찾는 과정중(while문)에서 focusNode 는 계속해서 바뀌고 parent 노드는 여기서 기억해둔다

        if(key < focusNode.key) {
            isLeftChild = true;             // 지우려는 노드가 왼쪽에 있는 노드냐 기록용
            focusNode = parent.leftChild;
        } else {
            isLeftChild = false;            // 지우려는 노드가 오른쪽에 있는 노드냐 기록용
            focusNode = parent.rightChild;
        }

        // 찾으려는 노드가 없는 경우
        if(focusNode == null) {
            return false;
        }
    }


    Node replacementNode;
    // 지우려는 노드의 자식 노드가 없는 경우
    if(focusNode.leftChild == null && focusNode.rightChild == null) {
        if (focusNode == root)
            root = null;
        else if (isLeftChild)
            parent.leftChild = null;
        else
            parent.rightChild = null;
    }
    // 지우려는 노드의 오른쪽 자식노드가 없는 경우 (왼쪽 자식 노드만 있는 경우)
    else if(focusNode.rightChild == null) {
        replacementNode = focusNode.leftChild;

        if (focusNode == root)
            root = replacementNode;
        else if (isLeftChild)
            parent.leftChild = replacementNode;
        else
            parent.rightChild = replacementNode;
    }
    // 지우려는 노드의 왼쪽 자식노드가 없는 경우 (오른쪽 자식 노드만 있는 경우)
    else if (focusNode.leftChild == null) {
        replacementNode = focusNode.rightChild;
        if (focusNode == root)
            root = replacementNode;
        else if (isLeftChild)
            parent.leftChild = replacementNode;
        else
            parent.rightChild = replacementNode;
    }
    // 지우려는 노드의 양쪽 자식노드가 모두 있는 경우
    // 오른쪽 자식 노드의 sub tree 에서 가장 작은 노드를 찾아서 지우려는 노드가 있던 자리에 위치시킨다
    else {
        // 삭제될 노드의 오른쪽 sub tree 를 저장해둔다
        Node rightSubTree = focusNode.rightChild;

        // 삭제될 노드 자리에 오게 될 새로운 노드 (오른쪽 sub tree 에서 가장 작은 값을 가진 노드)
        // 이 노드는 왼쪽 child 가 없어야 한다 (가장 작은 값이기 때문에)
        replacementNode = getRightMinNode(focusNode.rightChild);

        if (focusNode == root)
            root = replacementNode;
        else if (isLeftChild)
            parent.leftChild = replacementNode;
        else
            parent.rightChild = replacementNode;

        replacementNode.rightChild = rightSubTree;
        // 지우려는 노드의 오른쪽 sub tree 에 노드가 하나밖에 없는 경우
        if (replacementNode == rightSubTree) 
            replacementNode.rightChild = null;

        replacementNode.leftChild = focusNode.leftChild; // 지우려는 노드의 왼쪽 sub tree 를 연결시킨다
    }

    return true;
}

private Node getRightMinNode(Node rightChildRoot) {
    Node parent = rightChildRoot;
    Node focusNode = rightChildRoot;

    while (focusNode.leftChild != null) {
        parent = focusNode;
        focusNode = focusNode.leftChild;
    }

    parent.leftChild = null;
    return focusNode;
}
```



### 시간 복잡도

이진 탐색 트리의 삽입, 검색, 삭제에 대한 시간 복잡도는 **균형 상태이면 O(logN)**, **불균형 상태라면 최대 O(N)** 입니다.

<p align="center"><img src="./img/BST_time_complexity.png" width="600"></p>





## 이진 탐색트리가 중복된 데이터를 갖지 않는 이유

이진 탐색 트리는 목표 데이터 값의 빠른 탐색을 위한 자료구조입니다. 만약 이진 탐색 트리가 중복된 데이터 값을 가진다면 이진 탐색 트리에서 데이터를 탐색할 때 두가지 경우를 생각해야 할 것입니다.

1. 처음으로 발견되는 노드만 찾고, 중복 노드가 존재할 가능성을 무시한다.
   - 그렇다면 이진 탐색트리는 전혀 사용되지 않을 노드를 갖게 될 것입니다. 이는 메모리 낭비에 불과하며 잘못된 자료구조입니다.
2. 중복 노드가 존재할 가능성이 있으므로 중복 노드를 모두 탐색한다.
   - 그렇다면 일치하는 데이터를 찾았지만 중복 노드를 찾기 위해서 계속 탐색을 진행할 것입니다. 이는 시간의 낭비이며 빠른 탐색에 맞지 않는 자료구조입니다.



#### 관련 문제

[백준 5639 이진 검색 트리](https://www.acmicpc.net/problem/5639)

[백준 18240 이진 탐색 트리 복원하기](https://www.acmicpc.net/problem/18240)



# 우선순위 큐와 힙

## 우선순위 큐

일반적으로 큐(Queue)는 먼저 들어온 데이터가 먼저 나가는 선입선출(First in-First out) 구조입니다.  하지만, 우선순위 큐는 데이터가 들어온 순서가 아닌 데이터의 **우선 순위가 높은 데이터가 먼저 나가는 큐**를 말합니다. 우선순위 큐는 **주로 힙(Heap)이라는 자료구조로 구현**합니다. 또한 우선순위 큐는 **데이터의 우선 순위를 비교해야 하므로 그 데이터는 Comparable 하거나 comparator를 생성자에 넣어서 비교 연산이 가능하도록** 해야 합니다.



## 힙 

힙(Heap)은 완전 이진 트리에 있는 노드 중에서 키 값이 가장 큰 노드나 가장 작은 노드를 찾게 만든 자료구조입니다. 힙은 이진탐색트리와 달리 중복된 값이 허용됩니다.

<p align="center"><img src="./img/max_heap.png" width="600"></p>

- 최대 힙
  - 키 값이 가장 큰 노드를 찾기 위한 완전 이진 트리
  - 부모 노드의 키 값 >= 자식 노드의 키 값
  - 루트 노드:  키 값이 가장 큰 노드



<p align="center"><img src="./img/min_heap.png" width="500"></p>

- 최소 힙
  - 키 값이 가장 작은 노드를 찾기 위한 완전 이진 트리
  - 부모 노드의 키 값 < 자식 노드의 키 값
  - 루트 노드: 키 값이 가장 작은 노드
- 힙에서는 루트 노드의 원소만을 삭제 할 수 있습니다.
  - 루트 노드의 원소를 삭제하여 반환한다.
  - 루트 노드를 다시 구한다.



## 힙 구현

힙은 일반적으로 배열을 이용하여 구현합니다. 힙은 완전 이진 트리이므로 중간에 비어있는 요소가 없기 때문에 배열의 공간을 모두 사용하기 때문입니다.

<p align="center"><img src="./img/heap.png" width="600"></p>

**자식노드를 구하고 싶을 때**

- 왼쪽 자식노드 index = (부모 노드 index) * 2
- 오른쪽 자식노드 index = (부모 노드 index) * 2 + 1

**부모노드를 구하고 싶을 때**

- 부모 노드 index = (자식노드 index) / 2



### 삽입

힙에 데이터를 삽입하는 방법은 다음과 같습니다.

1. 완전 이진트리의 마지막 노드에 이어서 새로운 노드를 추가한다.
2. 추가된 새로운 노드를 부모의 노드와 비교하여 교환한다.
3. 힙 구조를 만족할 때 까지 2를 반복한다.

힙에 3을 삽입하는 과정은 다음과 같습니다.

<p align="center"><img src="./img/heap_insert.png" width="500"></p>

### 삭제

힙에서 데이터를 삭제하는 방법은 다음과 같습니다.

1. 루트 노드가 가장 우선 순위가 높으므로 루트 노드를 삭제한다.
2. 루트 노드가 삭제된 빈자리에 완전 이진트리의 마지막 노드를 가져온다.
3. 루트 자리에 위치한 새로운 노드를 자식 노드와 비교하여 교환한다.
4. 힙 구조를 만족할 때 까지 3을 반복한다.

<p align="center"><img src="./img/heap_delete.png" width="500"></p>



## 힙의 시간 복잡도와 우선순위 큐를 힙으로 구현하는 이유

우선순위 큐 == 힙이 아닙니다. 힙은 우선순위 큐를 구현할 수 있는 여러 자료구조 중 하나입니다. 하지만 우선순위 큐는 보통 힙으로 구현합니다. 그 이유는 시간 복잡도에서 가장 유리하기 때문입니다.

만약 배열로 구현한다면 우선 순위가 높은 순서대로 배열의 가장 앞부분부터 넣을 때 우선 순위가 높은 데이터는 맨 앞의 index를 이용하는 것으로 쉽게 찾을 수 있습니다. 하지만 우선 순위가 중간인 데이터를 삽입한다면 삽입하는 위치를 찾고, 뒤의 데이터를 모두 한 칸 씩 밀어야 합니다. 그러므로 정렬된 배열에서는 삽입은 O(n), 삭제는 O(1)의 시간 복잡도를 가집니다. 정렬되지 않은 배열이라면 삽입은 O(1)이고, 삭제할 때 삭제할 값을 찾아야 하므로 O(n)의 시간 복잡도를 가집니다.

연결리스트로 구현할 때도 배열과 크게 다르지 않습니다. 정렬 유무에 따라 배열과 같은 시간 복잡도를 가집니다.

하지만 힙으로 구현한다면  삽입과 삭제과정에서 모두 부모 노드와 자식 노드 간의 비교만 이뤄지므로 O(log2n)의 시간 복잡도를 가집니다. 

정리하면 배열과 연결리스트는 정렬 여부에 따라 삽입과 삭제에서 O(n)과 O(1)의 시간 복잡도를 가지게 되고, 힙은 일관적으로 O(log2n)의 시간복잡도를 가집니다. 그래서 편차가 심한 배열과 연결리스트 보다는 힙으로 구현을 합니다.



#### 관련 문제

[백준 11279 최대 힙](https://www.acmicpc.net/problem/11279)

[백준 1655 가운데를 말해요](https://www.acmicpc.net/problem/1655)

[백준 11000 강의실 배정](https://www.acmicpc.net/problem/11000)



# 그래프
# 그래프의 표현과 정의
어떤 자료나 개념을 표현하는 정점(vertex)들의 집합 V와 이들을 연결하는 간선(edge)들의 집합 E로 구성된 자료구조

주로 현실 세계의 사물이나 추상적인 개념 간의 연결관계를 표현할 때 사용

## 그래프 관련 용어

- 노드 (Node): 위치를 말함, 정점(Vertex)라고도 함

- 간선 (Edge): 위치 간의 관계를 표시한 선으로 노드를 연결한 선이라고 보면 됨 (link 또는 branch 라고도 함)
- 인접 정점 (Adjacent Vertex) : 간선으로 직접 연결된 정점(또는 노드)
- 참고
  - 정점의 차수 (Degree): 무방향 그래프에서 하나의 정점에 인접한 정점의 수
  - 진입 차수 (In-Degree): 방향 그래프에서 외부에서 오는 간선의 수
  - 진출 차수 (Out-Degree): 방향 그래프에서 외부로 향하는 간선의 수
  - 경로 길이 (Path Length): 경로를 구성하기 위해 사용된 간선의 수
  - 단순 경로 (Simple Path): 처음 정점과 끝 정점을 제외하고 중복된 정점이 없는 경로
  - 사이클 (Cycle): 단순 경로의 시작 정점과 종료 정점이 동일한 경우

## 그래프의 종류

<img width="731" alt="그래프_종류" src="https://user-images.githubusercontent.com/16794320/127731827-67d5dabf-871b-4b8c-a150-07b1749593a8.png">

### 방향 그래프

그래프의 각 간선이 방향이라는 속성을 갖는 그래프

### 가중치 그래프

그래프의 각 간선이 가중치(weight)라는 송석을 갖는 그래프

### 다중 그래프

두 정점 사이에 두 개 이상의 간선이 있을 수 있는 그래프

### 트리

간선을 통해 두 정점을 잇는 방법이 딱 하나밖에 없는 그래프

### 이분그래프

그래프의 정점들을 겹치지 않는 두 개의 그룹으로 나눠서 서로 다른 그룹에 속한 정점들 사이에만 간선이 존재하도록 만들 수 있는 그래프

### DAG

사이클 없는 방향 그래프(Directed Acyclic Graph)

기본적으로 방향 그래프, 한 점에서 출발해 자기 자신으로 돌아오는 경로가 없는 경우

## 그래프의 경로

그래프에서 경로란 끝과 끝이 연결된 간선들을 순서대로 나열한 것
<img width="303" alt="그래프_경로" src="https://user-images.githubusercontent.com/16794320/127731829-9ac04786-3ff6-4711-b67f-0e3f7f12233c.png">

주어진 그림에서 1에서 5로 가는 경로는 
(1,2),(2,4),(4,5)와 같이 표현.
간단하게 1-2-4-5로도 표현

## 그래프의 표현 방법

V = 정점의 수

### 인접 리스트

그래프의 각 정점마다 해당 정점에서 나가는 간선의 목록을 저장해서 그래프를 표현

각 정점마다 하나의 연결 리스트를 갖는 방식으로 구현

### 인접 행렬

인접 리스트가 두 정점의 연결 관계를 확인하기위해 모든 리스트를 뒤져야한다는 단점 보완

|V|X|V| 크기의 행렬(|V|는 정점의 갯수)로 표현한다.

간**선의 수가 $V^2$에 비해서 훨씬 적은 경우 인접리스트를 사용하는 것이 유리하고**

**간선의 수가 $V^2$에 비례하는 경우 인접행렬을 사용하는 것이 유리하다.**

### 암시적 그래프 표현

그래프를 직접 메모리에 표현하지않고 그래프 구조만 사용하는 것이 유리한 경우

- 입력이 그래프의 형태를 띄지않는 문제의 경우(ex. 배열로 주어진 미로)
- 그래프의 크기가 아주 큰데 실제 사용하는 부분은 그래프의 일부분인 경우

# DFS

DFS(Depth-First Search,깊이 우선 탐색)은 그래프의 모든 노드를 탐색하는 가장 단순한 방법입니다.

정점의 자식들을 먼저 탐색하는 방식으로 다음의 순서를 따릅니다.

1. 현재 정점과 인접한 간선들을 하나씩 검사한다.
2. 아직 방문하지 않은 정점으로 향하는 간선이 있다면 그 간선을 따라간다.
3. 더 이상 갈 곳이 없는 막힌 정점에 도달할 때까지 반복한다.
4. 더이상 갈 곳이 없다면 가장 마지막에 지난 간선을 따라 돌아가 더 이상 방문할 정점이 없을 때까지 반복한다.

각 정점이 정수형인 경우를 예시로 설명하겠습니다.


![그래프_표](./img/graph_chart.png)

위와 같이 만들어진 그래프를 DFS로 탐색하는 그림은 다음과 같습니다.

<img src="./img/graph_DFS.png" alt="그래프_DFS" style="zoom:50%;" />


## Java로 그래프를 표현하는 방법

정점의 개수를 n, 간선의 개수를 m,  연결관계에 있는 노드를 (node1, node2)의 순서쌍으로 하면, 다음과 같이 그래프를 표현할 수 있습니다.

```java
Map<Integer, ArrayList<Integer>> graph = new TreeMap<Integer, ArrayList<Integer>>();
int n = 0, m = 0
Scanner sc = new Scanner(System.in);
n = sc.nextInt();
m = sc.nextInt();
//초기화 해줘야지 아래의 반복문에서 nullPointException 발생하지않는다.
for(int i = 0;i<n;i++){
  graph.put(i+1,new ArrayList<>());
}
for(int i = 0;i<m;i++){
  int n1 = 0, v1 = 0;
  node1 = sc.nextInt();
  node2 = sc.nextInt();
  graph.get(n1).add(node2);
  graph.get(node2).add(node1);
}
```



## DFS 알고리즘 구현

스택을 활용해서 구현할 수 있습니다.

```java
//code
public void dfsWithoutRecursion(int start) {
  Stack<Integer> stack = new Stack<Integer>();
  boolean[] isVisited = new boolean[adjVertices.size()];
  stack.push(start);
  while (!stack.isEmpty()) {
    int current = stack.pop();
    isVisited[current] = true;
    visit(current);
    for (int dest : adjVertices.get(current)) {
      if (!isVisited[dest])
        stack.push(dest);
    }
  }
}
```

재귀호출을 통해 메서드 스택을 이용해서 구현하는 방법도 있습니다.

```java
public void dfs(int start) {
  boolean[] isVisited = new boolean[adjVertices.size()];
  dfsRecursive(start, isVisited);
}
void dfsRecursive(int current, boolean[] isVisited) {
  isVisited[current] = true;
  visit(current);
  for (int dest : adjVertices.get(current)) {
    if (!isVisited[dest])
      dfsRecursive(dest, isVisited);
  }
}
```



## 시간 복잡도

일반적으로 DFS의 시간복잡도는 정점의 수를 V, 간선의 수를 E라고 할 때 O(V+E) 입니다.
