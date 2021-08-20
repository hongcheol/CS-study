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
   3. [BFS](#BFS)
   4. [최단 경로 알고리즘](#최단 경로 알고리즘)
      1. [다익스트라](##다익스트라)
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

# BFS

BFS(너비 우선 탐색)은 그래프를 탐색하는 방법 중 하나입니다.

정점들과 같은 레벨에 있는 노드(형제 노드)들을 먼저 탐색하는 방법으로 다음의 순서를 따릅니다. 

1. 현재 정점과 인접한 간선들을 하나씩 검사합니다.

2. 현재 노드에서 방문할 수 있는 노드를 전부 방문합니다.

3. 전부 방문한 후 그 다음 레벨의 노드를 방문합니다.

4. 더 이상 방문할 곳이 없다면 탐색을 종료합니다.

   

   ![그래프_표](./img/graph_chart.png)

<img src="./img/graph_BFS.png" alt="graph_BFS" style="zoom:50%;" />

## BFS 알고리즘 구현

queue와 배열을 이용해서 구현할 수 있습니다.

```java
//code
static void bfs(Map graph,int start_node){
  Queue<Integer> need_visit = new LinkedList<>();
  Queue<Integer> visited = new LinkedList<>();
  need_visit.add(start_node);

  while(need_visit.isEmpty()==false){
    int node = need_visit.poll();
    if(!visited.contains(node)){
      visited.add(node);
      ArrayList<Integer> temp = (ArrayList<Integer>) graph.get(node);
      for(int data : temp) need_visit.add(data);
    }
  }
  for(int data : visited)System.out.print(data+" ");
}
```



## 시간복잡도

일반적으로 BFS의 시간복잡도는 정점의 수를 V, 간선의 수를 E라고 할 때 O(V+E) 입니다.

# 최단 경로 알고리즘

### 최단 경로 문제란 무엇일까요?

최단 경로 문제란 두 노드를 잇난 가장 짧은 경로를 찾는 문제입니다. 즉, 가중치 그래프에서 간선의 가중치의 합이 최소가 되도록하는 경로를 찾는 것입니다.

그렇다면 최단 경로 문제는 어떤 종류가 있을까요??

1. 단일 출발 및 단일 도착 최단 경로 문제
2. 단일 출발 최단 경로 문제
3. 전체 쌍 최단 경로 문제

## 다익스트라

다익스트라 알고리즘은 최단 경로의 문제 종류 중 단일 출발 최단 경로 문제에 속합니다. 

그 이유는 다익스트라 알고리즘이 하나의 정점에서 다른 모든 정점 간의 가장 짧은 거리를 구하는 알고리즘이기 때문입니다.

### Basic idea

```java
//init
S = {v0}; distance[v0] = 0; 
for (w 가 V - S에 속한다면)//V-S는 전체 정점에서 이미 방문한 정점을 뺀 차집합.
	if ((v0, w)가 E에 속한다면) distance[w] = cost(v0,w); 
	else distance[w] = inf;

//algorithm
S = {v0};
while V–S != empty {
  //아래의 코드는 지금까지 최단 경로가 구해지지않은 정점 중 가장 가까운 거리를 반복한다는 의미
	u = min{distance[w], w 는 V-S의 원소};//---1
	
  S = S U {u}; 
  V–S = (V–S)–{u};
	for(vertex w : V–S)
		distance[w] = min{distance[w],distance[u]+cost(u,w)}//update distance[w];---2
}
```

위의 코드에서 1로 마킹한 부분에대한 설명을 하겠습니다.

먼저 length[v]는 최단 경로의 길이를 의미하고 distance[w]는 시작점에서부터의 최단 경로의 길이를 의미합니다.

S는 시작점을 포함해서 현재까지 최단 경로를 발견한 점들의 집합을 의미합니다.

<img src="./img/Dijkstra_0.png" alt="Dijkstra_0" style="zoom:75%;" />

u = min{distance[w], w 는 V-S의 원소}의 의미는 u까지의 최단 거리의 길이는 distance[u] 와 같다는 의미입니다. 

둘이 같다는 것은 다음처럼 증명할 수 있습니다.

![Dijkstra_1](./img/Dijkstra_1.png)

1. distance[u] > length[u]라고 가정해보겠습니다.
2. P를 시작점부터 u까지의 최단 경로라고 하면
3. P의 길이 = length[u]입니다.
4. P는 S에 속하지않는 최소 1개의 정점을 가집니다. 그렇지않다면 P의 길이 = distance[u]가 됩니다.
5. P의 경로에 속하면서 S에 속하지않는 시작점에서 가장 가까운 점을 w라 하겠습니다.
6. 그렇게 된다면 distance[u] > length[u] >= length[w] 가 성립하고, length[w] = distance[w]이기 때문에  이로부터
   **distance[u] > distance[w]**를 유추해낼 수 있습니다.
7. 이는 다익스트라 알고리즘은 아직 최단경로가 찾아지지않은 정점들만 선택한다는 정의에 어긋납니다.(지금 보는 점보다 가까운 경로가 존재한다면 이전 탐색에서 걸러져서 S에 포함되어있기 때문입니다.)

그렇기 때문에 distance[u] = length[u]입니다.

다음으로는 distance[w]를 업데이트하는 부분입니다.

<img src="./img/Dijkstra_2.png" alt="Dijkstra_2" style="zoom:50%;" />

위의 그림에서와 같이 시작점 v0에서 u를 거쳐 w로 가는 경로와 v0에서 w로 가는 경로의 길이 중 가까운 경로를 distance[w]로 설정하고 S와 V-S를 최신화해줍니다.

distance[ ]를 업데이트할 때 각각의 간선들이 2번씩 체크되는데 그 이유는 다음과 같습니다.

1. 

<img src="./img/Dijkstra_3.png" alt="Dijkstra_3" style="zoom:75%;" />

아직 최단 경로를 찾지 못한 정점 중 u에 인접한 정점들의 거리를 업데이트 할 때 u와 w를 연결하는 간선이 체크가 됩니다. 

2.

<img src="./img/Dijkstra_4.png" alt="Dijkstra_4" style="zoom:75%;" />

정점 w의 최단 경로가 찾아졌기 때문에, w는 S에 속하게됩니다. 아직 최단 경로를 찾기 못한 정점들에서 w까지의 거리를 업데이트하는 과정에서 u에서 w로의 간선이 다시 한 번 체크됩니다.



이런 방식으로 코드를 작성하면 각 정접들에대해서 u에 인접한 모든 간선을 살펴보는 연산이 추가되기 때문에 최악의 경우 시간복잡도가 O(𝑛^2)가 되버립니다. 이를 개선하는 방법으로는 대표적으로 우선순위 큐를 사용하는 방법(O( 𝑛 + 𝑚 log 𝑛))과 피보나치 힙을 사용하는 방법(O(𝑛 log 𝑛 + 𝑚))이 있습니다. 

피보나치 힙에대한 내용은 [피보나치 힙](https://en.wikipedia.org/wiki/Fibonacci_heap) 을 확인해주시고, 지금은 우선순위 큐에대한 내용을 다루겠습니다.

### 다익스트라 알고리즘 로직(우선순위 큐)

- 첫 번째 정점을 기준으로 연결되어있는 정점들을 추가해가면서 최단 거리를 갱신합니다.

  첫 정점부터 각 노드 사이의 거리를 저장하는 배열을 만든 후에 첫 정점의 인접 노드 간의 거리부터 먼저 계산하면서, 첫 정점부터 해당 노드 사이의 가장 짧은 거리를 해당 배열에 업데이트 합니다. 이런 로직은 현재의 정점에서 갈 수 있는 정점들부터 처리한다는 점에서 BFS와 비슷합니다. 

  다양한 다익스트라 알고리즘이 있지만 가장 개선된 형태인 우선순위 큐를 사용하는 방식을 다뤄보겠습니다.

  먼저 우선 순위 큐를 간단하게 설명하면 MinHeap 방식을 사용해서 현재 가장 짧은 거리를 가진 노드 정보를 먼저 꺼냅니다.

  꺼낸 노드는 다음의 과정을 반복합니다.

  1. 첫 정점을 기준으로 배열을 선언핸 첫 정점에서 각 정점까지의 거리를 저장합니다.
     - 초기에는 첫 정점의 거리를 0, 나머지는 무한대(inf)로 저장합니다.
     - 우선순위 큐에 순서쌍 (첫 정점, 거리 0) 만 먼저 넣어줍니다. 
  2. 우선순위 큐에서 노드를 꺼냅니다.
     - 처음에는 첫 정점만 저장된 상태이기때문에 첫번째 정점만 꺼내집니다.
     - 첫 정점에 인접한 노드들 각각에대해서 첫 정점에서 각 노드로 가는 거리와 현재 배열에 저장되어있는 첫 정점에서 각 정점까지의 거리를 비교합니다.
     - 배열에 저장되어 있는 거리보다, 첫 정점에서 해당 노드로 가는 거리가 더 짧은 경우, 배열에 해당 노드의 거리를 업데이트 합니다.
     - 배열에 해당 노드의 거리가 업데이트된 경우, 우선순위 큐에 해당 노드를 넣어줍니다.
  3. 2번의 과정을 우선순위 큐에서 꺼낼 노드가 없을 때까지 반복합니다.

- 우선순위 큐를 사용하면 지금까지 발견된 가장 짧은 거리의 노드에대해서 먼저 계산을 해서 더 긴 거리로 계산된 루트에 대해서는 계산을 스킵할 수 있다는 장점이 있습니다.

pseudo 코드는 다음과 같습니다. 

```java
found[], distance[]를 초기화
construct min_heap(V-{s});
for(i = 0; i < n-2; i++) { //𝑛−1iterations(=Θ(𝑛))---(1) 
  distance[u]가 최소인 정점 u를 선택합니다. //Θ(1) found[u] = T;로 바로 배열로 접근 가능
  min_heap에서 정점 u를 제거; //O(log𝑛)---(2)
  for(every vertex w adjacent to u) //Θ(𝑚)total---(a)
      if(found[w] == F && distance[u] + cost(u,w) < distance[w]){
        distance[w] = distance[u] + cost(u,w);
        adjust heap(w); //𝑂(log𝑛)foreachedgecheck---(b) 
  } // distance[w]가 수정됐기 때문에 heap을 조정해주는 for문
}
```



### 시간 복잡도

위의 pseudo code에서 다음 2가지 과정을 거칩니다.

(1),(a) - 각 정점마다 인접한 간선들을 모두 검사하는 과정 -> 𝑂(𝑛)

(2),(b) - 우선순위 큐에 정점/거리 정보를 넣고 삭제하는 과정 -> 𝑂(log𝑛)

따라서 전체 알고리즘은 O((𝑛+𝑚)log𝑛)입니다.

