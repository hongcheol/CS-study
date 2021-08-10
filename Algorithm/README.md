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
	2. 이진 검색트리
	3. 우선순위 큐와 힙
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
