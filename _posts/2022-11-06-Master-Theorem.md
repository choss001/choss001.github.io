### Master Theorem  

종만북을 공부하다가 4챕터에서 재귀 알고리즘의 시간 복잡도를 계산하는데 Master Theorm의 공식이 나왔는데  
책에는 생략이 되어있어서 한번 찾아보게 되었습니다.  

</br>
시간 복잡도는 알고리즘을 풀때 고려해야될 사항중에 하나인데요 자신이 적용해야될 알고리즘의 시간복잡도를 대략 계산해서
지금 생각하고 있는 알고리즘으로 풀지 다른 방법을 찾아야할지 결정할 수 있는 하나의 도구입니다.  
만약 시간 복잡도를 고려 안하고 일단 구현했다면 나중에 생각보다 오래걸려서 다 구현 해놓고도 
처음부터 완전히 다른 알고리즘으로 바꿔서 다시 풀어야되는 사태가 일어날 수도 있습니다.  

지금 제가 포스트 할 Master Theorem이 바로 재귀 알고리즘 시간복잡도를 계산하기 위한 공식입니다.  

master method는 반복 관계 형식을 해결하기 위한 공식입니다.
```
T(n) = aT(n/b) + f(n),

n = input 사이즈
a = 재귀 안에서 subproblem(하위 문제)
n/b = 각각의 subproblem의 사이즈 . 모든 subproblems은 같은 사이즈를 갖고 있다고 가정한다.
f(n) = 재귀 호출 외부에서 어떠한 일이 수행되어지는 코스트, 
      이것은 문제를 나누는 코스트를 포함하고 솔루션을 합치는 코스트를 포함합니다.
      
a ≥ 1 and b > 1 둘다 상수, f(n) is an asymptotically positive function.

```

asymptotically positive function은 충분히 큰 n에 대해서 f(n) > 0 이라는것을 의미한다.

```
T(n) = aT(n/b) + f(n)

where, T(n) has the following asymptotic bounds:

    1. If f(n) = O(nlogb a-ϵ), then T(n) = Θ(nlogb a).

    2. If f(n) = Θ(nlogb a), then T(n) = Θ(nlogb a * log n).

    3. If f(n) = Ω(nlogb a+ϵ), then T(n) = Θ(f(n)).

ϵ > 0 is a constant.
```  
위에 각각의 조건들은 다음과 같이 해석될 수 있습니다.  
1. 만약 각각의 단계에서 하위 문제의 푸는 비용이 특정한 요소에 의해서 증가한다면  
f(n)의 벨류는 nlogb 보다 작아진다 그러므로 시간복잡도는 마지막 단계의 비용의 영향을 받는다 ie. nlogb a
3.


