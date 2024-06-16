---
lastmod: 2024-06-16
---
올것이 왔다 weighted shortest path. 이제 수업 내용은 weighted shortest path를 이용해 최단거리를 구하는 알고리즘에 대해서 알아본다. Sorting부분에서도 그랬듯, 하나의 내용에 대해 다각도로 분석하고 다양한 알고리즘을 비교분석하는게 주된 내용이다.

# 1 Weighted Graph 정의
> [!summary]
> Graph + Weight = Weighted Graph

건조하게 이야기하면 Graph에 Weight를 추가한 것이 Weighted Graph다. 앞에서 Graph를 $G=(V,E)$라고 썼다. 그럼 Weight를 추가하면 된다. 수학 기호를 대동해보면 $W=E \rightarrow {\rm I\!R}$라고 쓴다. 풀어서 설명하면, **==Edge에 대한 숫자(리얼넘버)==** 라고 표현한다. 무슨말이냐, 이전 까지는 Adjacency list가 표현해야 할 것이 노드간ㄷ의 연결관계인 Edge였다. 그러나 이제는 거기에 각 노드간 연결 강도를 추가하는 것이다. 교재에 나온 예시를 가져오면 아래와 같다

```python
w2 = {
	0:{
		1:1,
		3:2,
		4:-1,
	},
	1:{0:1},
	2:{3:0},
	3:{
		2:0,
		0:2
	},
	4:{0:-1}
}
```

위 구조를 뜯어보면 다음과 같다.가장 바깥 dictionary의 key는 노드의 id가 된다. 각 value에 해당하는 dictionary의 key는 해당 바깥 dictionary의 연결된 node를 뜻한다. 그리고 value는 weight를 뜻한다. 가령 `w2[0][1]`은 `0`번 노드에서 `1`번 노드를 이어주는 edge의 weight를 뜻한다. 글로 설명하기 어려운 부분을 다시 그림으로 그려보면 아래와 같다.

![[Pasted image 20240604230857.png]]

`w2`대로 해보면 위 값들이 맞는 것을 볼수 있다. 예를들어 `w[3][2] -> 0`인데 위의 그림에서 3->0으로 이어진 edge의 weight가 0임을 확인할 수있다.

# 2 Weighted paths
한 path의 weight란 path를 이루는 edge의 합을 뜻한다. 나도 슬슬 쓰면서 헷갈리는 부분이 있다. 노드와 노드 사이를 연결하는 edge의 여러가지 조합을 paths라고 일단 정의하고 글을 진행해보겠다. 그러면 path란 여러 어떤 노드 `s` 어떤 노드 `t` 를 연결해주는 어떤 edge의 조합이라고 보면 되겠다. 

# 3 Negative weight
만약 두 노드간 음수가 있는 cycle이 있다면 어떻게 될까? 그렇게 되면 해당 부분만 돌게 되며, 수학적으로는 최단거리가 되지만 음의 무한대인 최단 거리가 된다. 이는 논리적으로 틀리기 때문에 이런경우를 undefined라고 한다. 반면 노드와 노드 사이가 연결되지 않은 경우를 양의 무한대로 정의한다. 이는 수학적 귀납법상 편의를 위해서다.

![[IMG_4195.jpeg]]

# 4 Relaxation
> [!info] relaxation을 이해하고 SSSP(Single Source Shortest Path)에서 Relaxation을 이해하는 과정
> 여기서는 DAG에 대한 Relaxation

**relaxation algorithm 이란?**
- 최적값을 찾는 알고리즘을 뜻함. 어떤 문제에 대해 최적이 아닌 상태에서 시작해 반복적으로 알고리즘을 수행해 최적의 해결책을 찾는 알고리즘
	- Rhino + Grasshopper의 캥거루
	- 아마도 hill climbing과 같은 것들도? gradient descent

**SSSP에서 Relaxation**
- 최단 거리의 weight를 찾는 것
- $\delta(s,v)$는 source `s` vertex `v`까지의 최단거리의 weight를 뜻함
	- 그러니까 $\delta(s,v)$ 를 찾으면 된다는 말씀!
- 그럼 non optimal한 상태는 무엇이냐
	- `s`에서 `v`까지 거리의 추정치를 $d(s,v)$라고 쓴다
	- $d(s,v)$를 우선 모두다 최대값으로 설정한다
		- $d(s,s)$ ==자기자신까지 거리는 0이니까 여기는 주의해주자==
- Relax
	- $d(s,v)$ 가 $\delta(s,v)$ 가 될때까지 **relax**를 해준다!
	- $d(s,v)=\delta(s,v)$ 가 됬을 때 **fully relaxed**라고 표현한다고 그러네

그럼 위 내용을 pseudo code로 표현하면 아래와 같다

```python
def general_relax(Adj, w, s): #Adj: adjacency list, w: weight(?), s: start
	d = [float('inf') for _ in Adj] # s로 부터 각 노드에 대한 d(s,v)
	parent = [None for _ in Adj] # parent tree를 또 만드려고 그러네?
	d[s], parent[s] = 0, s # 자기 자신의 거리는 0, 자기자신의 부모는 자기자신
	while True:
		relax some d[v] ?? # 여기에 relaxation
	return d,parent
```
> [!question] 위 알고리즘은 DAG를 가정하고 하는거였나??
> Yes.

**이제 알아봐야 할 것은 언제 알고리즘을 종료하고, 어떻게 relax를 하는지 알아봐야함**

수업에서는 귀납법을 사골우리듯 계속 사용한다. 그러나 나는 아직 귀납법이 잘 이해가 되지는 않는다. 그러므로 그림을 그려본다. vertex `v`에 대해서, $\delta(s,v)$가 진짜 SSSP라면, `v`향해 들어오는 그 어떤 노드 `u`로 부터 weight를 계산해도 그 값보다 작아야 한다. 다시 말하면 $\delta(s,v) > d(s,u)+ w(u,v)$라면 $\delta(s,v) = d(s,u)+ w(u,v)$ 로 업데이트를 해줘야한다ㅏ. 아래 그림을 살펴보면 대략 감을 잡을 수 있다.

![[Pasted image 20240608142605.png]]


이제 위 알고리즘을 코드로 나타내면 아래와 같다
```python
def try_to_relaxA(Adj,w,d,parent,u,v):
	if d[v] > d[u] + w(u,v):
		d[v] = d[u] + w(u,v)
		parent[v] = u
```
뒤에는 수학증명이 나오는데 어렵다. 넘어가자


# 5 DAG Relaxation
음 중간에 어려운 부분을 떼놓고 직관에 의존해 이야기해보면 이렇다.
1. DAG에는 Negative Cycle이 존재하지 않는다
2. 따라서 DAG Relaxation은 반드시 끝나게 되어있다
3. (여기는 잘 이해가 안가지만) 한 시작 노드에 대해 topo sort를 한 후 이 순서대로 relaxation을 수행하면 최단거리를 구할 수 있다
4. 위 1,2,3의 과정을 거치는 알고리즘을 `DAG Relaxation`이라 한다

대략 내 말로 해보면 이정도로 풀이해볼 수 있다. DAG는 Cycle이 없다. 따라서, 정렬을 수행할 수 있다. 이전 챕터에서 배운 DFS를 이용하면 topo sort를 할 수 있다. topo sort는 단순히 순서만 하기 떄문에 여기에 weight를 계산하는 relaxation을 더하면 DAG Relaxation을 통해 최단 거리를 구할 수 있다.

```python
def DAG_Relaxation(Adj, w,s):
	_,order = dfs(Adj,s)
	order.revers()
	d = [float('inf') for _ in Adj]
	parent = [None for _ in Adj]
	d[s], parent[s] = 0, s
	for u in order:
		for v in Adj[u]:
			try_to_relax(Adj,w,d,parent,u,v)
	return d,parent
```

