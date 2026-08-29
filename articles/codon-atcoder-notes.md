---
title: "Codonで競プロするときのPythonとの違い・注意点"
emoji: "🐍"
type: "tech"
topics: ["codon", "python", "atcoder", "競技プログラミング"]
published: false
---

# 入出力・基礎機能

```python
grid = [[int(x) for x in input()] for _ in range(h)]
```

Codonでは再帰DFSが遅い・深さで落ちる場合があるので、基本はstackで書くと安全です。

```python
def dfs(start: int, graph: list[list[int]]) -> list[int]:
    n = len(graph)
    used = [0] * n
    stack = [start]
    used[start] = 1
    order = []

    while stack:
        u = stack.pop()
        order.append(u)

        for v in graph[u]:
            if not used[v]:
                used[v] = 1
                stack.append(v)

    return order
```

Pythonと違って、Codonでは負の値の `%` が負になることがあります。非負の剰余として使いたいときは補正します。

```python
def mod_norm(x: int, m: int) -> int:
    r = x % m
    if r < 0:
        r += m
    return r
```

Codonは現時点では完全なNumPy/SciPy互換ではありません。

Codonではlistがhashableなので、dictのkeyにしたりsetに入れたりできます。

Codonでは`0`/`1`と`False`/`True`は別の型です。

Codonの`int`は $[-2^{63}, 2^{63})$ で、C++の`long long`と同じ64bit符号付き整数です。最大値は $2^{63}-1 = 9223372036854775807 \simeq 9.22 \times 10^{18}$ です。

## 参考

- [Codon（AtCoder Language Update 2025 に際して） - いかたこのたこつぼ](https://ikatakos.com/pot/programming_algorithm/python_tips/codon)
