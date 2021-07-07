---
title: 순열 / 조합 백트래킹으로 구현하기 using Python
# date: 2021-07-05 12:00:00 +/-TTTT
categories: [Coding-Test, Algorithm]
tags: [순열, 조합]     # TAG names should always be lowercase
---
알고리즘 문제를 풀다보면 순열과 조합을 사용하여 모든 경우의 수를 구해봐야할 일이 많습니다. (완전탐색 / 브루트포스)  
파이썬에는 `itertools` 라이브러리에 해당 기능을 제공하지만 코딩테스트 환경에서는 미리 완성된 리스트를 하나부터 일일히 계산하면 시간초과 판정을 받을 수 있습니다.  
따라서 **백트래킹 기법**을 사용하여 각각의 경우의 수를 만들어가면서 계산하는 것이 좋습니다.

---
## 백트래킹 (재귀)
우선 본격적인 코드를 소개하기 전에 완전탐색에 사용되는 백트래킹 기법을 간단하게 소개하겠습니다.
```python
def back(n):
    if n==탈출조건:
        행동수행    # (ex. print / max / min / etc...)
        return

    변화주기
    back(n+1)      # 재귀호출
    복원하기
```
이런 식으로 모든 경우의 수를 차례차례 탐색할 수 있습니다.

## 순열 (<sub>n</sub>P<sub>r</sub>)
```python
arr = [1,2,3,4,5]       # n = 5, r = 3
visited = [False] * n

def perm():
    if len(picked) == r:
        print(picked)
        return

    for i in range(n):          # nPn의 경우 for문은 사용하지 않는다.
        if not visited[i]:
            picked.append(arr[i])   # 변화
            visited[i] = True
            perm()
            visited[i] = False
            picked.pop()            # 복원
```

## 조합 (<sub>n</sub>C<sub>r</sub>)
```python
arr = [1,2,3,4,5]       # n = 5, r = 3
visited = [False] * n

def comb(left):
    if len(picked) == r:
        print(picked)
        return

    for i in range(left, n):       # 조합의 경우 left ~ n까지만 탐색
        picked.append(arr[i])      # 변화
        visited[i] = True
        comb(i)                    # 마지막에 넣었던 index가 left
        visited[i] = False
        picked.pop()               # 복원
```


