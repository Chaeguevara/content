---
lastmod: 2024-06-04
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