---
title: "CodonでAtCoderするときのPythonとの違い・ハマりどころ"
emoji: "🐍"
type: "tech"
topics: ["codon", "python", "atcoder", "競技プログラミング"]
published: true
---

AtCoderでPythonっぽく書きつつ高速に動かせるCodonはかなり便利です。ただし、**Pythonのコードをそのまま持ってくれば必ず動くわけではありません**。

2026年6月16日時点のAtCoderの言語一覧では、`Python (Codon 0.19.3)` が使われています。

この記事では、自分が競プロ用にためていたCodonメモから、特に「Pythonとの違い」「Codonでハマりやすいところ」「AtCoderでよく使う書き方」を抜き出してまとめます。

## 先に重要な違い

| 項目 | Python | Codonでの注意 |
| --- | --- | --- |
| `int` | 任意精度整数 | 基本は64bit符号付き整数 |
| `bool` | `int`の派生型 | `int`とは別の型 |
| 負数の `%` | 正の法なら非負になる | 負になる場合があるので要注意 |
| 再帰 | `setrecursionlimit`で押し切ることも多い | 深いDFSはstack推奨 |
| `Fraction` | 使える | 少なくともAtCoderのCodonではそのまま使えない |
| `math.isqrt` | 使える | そのままでは使えない |
| `pow(a, -1, mod)` | 使える | そのままでは使えない |
| `functools.cmp_to_key` | 使える | そのままでは使えない |
| `list` | hashableではない | Codonではhashableなのでdictのkey等にできる |
| NumPy/SciPy | CPython向けに豊富 | 完全互換とは考えない方がよい |

「Pythonの文法で書ける高速言語」くらいに考えると楽です。**Pythonそのものではありません。**

# 入出力・基礎

## `input()`で普通に読む

Codonでは標準の`input()`自体が十分高速なので、Pythonほど`sys.stdin.readline`にこだわる必要はありません。

一方、型推論や`map`周りでコンパイルに詰まる場合は、リスト内包表記へ寄せると安定することがあります。

```python
n = int(input())
a, b = [int(x) for x in input().split()]
arr = [int(x) for x in input().split()]

grid = [[int(x) for x in input()] for _ in range(h)]
```

`map`が使えないわけではありません。

```python
n, m = map(int, input().split())
```

ただ、Codonで妙な型エラーが出たときは、まず内包表記へ直すと原因を切り分けやすいです。

## f-string

f-stringは普通に使えます。

```python
val_a = 100
val_b = 0.987654321
print(f"{val_a:06d}-{val_b:.6f}")
```

## `1e6`は整数ではない

これはPythonでも同じですが、

```python
x = 1e6
```

は`float`になります。整数が欲しいなら、

```python
x = 10**6
```

と書いておく方が安全です。

# list / dict / set / deque

## 2次元配列は内包表記

```python
h, w = 5, 3
dp = [[0] * w for _ in range(h)]
```

競プロではこれで十分です。

```python
odd = [i for i in range(100) if i % 2 == 1]
```

のような内包表記もCodonではかなり高速です。

## dictの存在判定

キーの存在判定は素直に

```python
if key in d:
    ...
```

と書くのがよいです。

`key in d.keys()`や`key in set(d.keys())`へわざわざ変換する必要はありません。

出現回数を数えるだけなら、

```python
d[a] = d.get(a, 0) + 1
```

で書けます。

## 文字列は`join`

長い文字列を

```python
ans += ch
```

で伸ばし続けるより、リストへ入れて最後に`join`する方が安全です。

```python
res = []
for ch in "abc":
    res.append(ch)

print("".join(res))
```

## Codonではlistがhashable

Pythonでは、

```python
{[1, 2]: 3}
```

のようにlistを辞書のkeyにすることはできません。

Codonではlistがhashableなので、dictのkeyにしたりsetへ入れたりできます。

これはPythonとの差がかなり大きい部分です。

## `0` / `1` と `False` / `True` は別の型

Pythonでは、

```python
True == 1
False == 0
```

が成立しますが、Codonでは`bool`と`int`は別の型として扱われます。

Pythonの「boolはほぼ0/1」という感覚で型を混ぜると詰まることがあります。

## set

よく使う操作は普通に使えます。

```python
s.discard(x)          # なければ何もしない
x = s.pop()           # 適当な1要素を削除して返す
s.update([4, 5])
s.difference_update([5, 6])
```

部分集合判定もできます。

```python
s1 = {1, 2}
s2 = {1, 2, 3}

print(s1 <= s2)
print(s1 < s2)
```

## deque

```python
from collections import deque

q = deque()
q.append(x)
q.appendleft(x)
x = q.pop()
x = q.popleft()
```

BFS用途なら特に困りません。

ただし、Pythonの`collections.deque`で使えるメソッドが全部そのまま使えるとは限りません。`remove`、`index`、`count`、`reverse`、`reversed`などへ依存するコードは、AtCoderへ出す前に確認した方が安全です。

# 整数とbit演算

## `int`は64bit

Codonの`int`はPythonの任意精度整数ではなく、基本的にC++の`long long`相当の64bit符号付き整数です。

範囲は

$$
-2^{63} \le x < 2^{63}
$$

で、最大値は

$$
2^{63}-1 = 9223372036854775807
$$

です。

Pythonなら普通にできる

```python
x = 1 << 100
```

のような巨大bit集合を、そのままCodonへ持ち込むのは危険です。

競プロでbit集合にするなら、だいたい60bit程度までを意識すると扱いやすいです。

## 基本的なbit演算

```python
x & y
x | y
x ^ y
~x

x <<= 1
x >>= 1
```

部分集合列挙も高速です。

```python
s = 13
sub = s

while sub:
    print(sub)
    sub = (sub - 1) & s
```

空集合も含めるなら、

```python
s = 13
sub = s

while True:
    print(sub)
    if sub == 0:
        break
    sub = (sub - 1) & s
```

です。

# `%`の符号に注意

Pythonと同じつもりで負数の剰余を扱うと事故ることがあります。

非負の剰余が必要なら、明示的に補正しておくと安全です。

```python
def mod_norm(x: int, m: int) -> int:
    r = x % m
    if r < 0:
        r += m
    return r
```

合同式や座標の循環などで特に注意します。

# Python標準ライブラリとの違い

## `Fraction`はそのまま使えない

Pythonなら、

```python
from fractions import Fraction
```

で有理数を扱えますが、Codonではそのまま使えません。

正確な有理数計算が必要なら、

- 分子・分母を自分で持つ
- `gcd`で約分する
- そもそもPyPyへ切り替える

あたりを考えた方が早いです。

## `math.isqrt`は使えない

Pythonでは整数平方根に`math.isqrt`がありますが、Codonでは使えません。

浮動小数点数で

```python
int(n ** 0.5)
```

とすると、大きな整数で境界を踏む可能性があります。

64bit整数全体を安全に扱うなら、例えば二分探索で書けます。

```python
def isqrt(n: int) -> int:
    if n < 0:
        raise ValueError("isqrt() argument must be nonnegative")

    if n < 2:
        return n

    # sqrt(2^63 - 1) < 3037000500
    lo = 0
    hi = 3037000500

    while lo + 1 < hi:
        mid = (lo + hi) // 2

        # mid * mid <= n と同じだが、積のoverflowを避ける
        if mid <= n // mid:
            lo = mid
        else:
            hi = mid

    return lo
```

## `pow(a, -1, mod)`は使えない

Pythonでは、

```python
pow(a, -1, mod)
```

でmod逆元を取れますが、Codonではそのまま動きません。

一般のmodなら拡張Euclidで書けます。

```python
def modinv(a: int, m: int) -> int:
    a %= m
    x, y = a, m
    u, v = 1, 0

    while y:
        t = x // y
        x, y = y, x - t * y
        u, v = v, u - t * v

    if x != 1:
        raise ValueError("inverse does not exist")

    return (u % m + m) % m
```

modが素数で`a % mod != 0`なら、フェルマーの小定理から

```python
pow(a, mod - 2, mod)
```

でも求められます。

## `math.gcd(*list)` / `math.lcm(*list)`

複数要素を`*`展開して一気に渡すコードは、Codonではうまく動かないことがあります。

素直にループすると確実です。

```python
import math

def gcd_list(a: list[int]) -> int:
    g = 0
    for x in a:
        g = math.gcd(g, x)
    return g


def lcm_list(a: list[int]) -> int:
    res = 1
    for x in a:
        res = math.lcm(res, x)
    return res
```

## `functools.cmp_to_key`は使えない

Pythonの

```python
from functools import cmp_to_key
```

に依存した比較関数ソートはCodonでは使えません。

可能なら

```python
a.sort(key=lambda x: x[1])
```

のようなkey関数へ落とします。

比較関数でしか表現しにくい順序なら、マージソート等を自前実装するか、問題によってはPyPyへ切り替える方が楽です。

## itertoolsは一度list化すると扱いやすい

```python
from itertools import permutations, combinations, product, accumulate

p = list(permutations(a))
c = list(combinations(a, 2))
prod = list(product([0, 1], repeat=3))
pref = list(accumulate(a))
```

Codonではイテレータのまま複雑に持ち回るより、競プロならlist化してしまった方がハマりにくいです。

# グラフでは再帰よりstack

Codonでは深い再帰DFSが遅かったり、深さで落ちたりする場合があります。

基本形はstackにしておくと安全です。

```python
def dfs(start: int, graph: list[list[int]]) -> list[int]:
    n = len(graph)
    used = [False] * n
    stack = [start]
    used[start] = True
    order = []

    while stack:
        u = stack.pop()
        order.append(u)

        for v in graph[u]:
            if not used[v]:
                used[v] = True
                stack.append(v)

    return order
```

## 経路復元つきDFS

```python
def dfs_path(start: int, goal: int, graph: list[list[int]]) -> list[int]:
    n = len(graph)
    prev = [-1] * n
    prev[start] = start
    stack = [start]

    while stack:
        u = stack.pop()

        if u == goal:
            break

        for v in graph[u]:
            if prev[v] == -1:
                prev[v] = u
                stack.append(v)

    if prev[goal] == -1:
        return []

    path = []
    cur = goal

    while cur != start:
        path.append(cur)
        cur = prev[cur]

    path.append(start)
    path.reverse()
    return path
```

## Union-Findもfindを反復で書く

`sys.setrecursionlimit`へ依存せず、経路圧縮をループで書くと安心です。

```python
class UnionFind:
    par: list[int]
    rank: list[int]

    def __init__(self, n: int):
        self.par = [i for i in range(n)]
        self.rank = [0] * n

    def find(self, x: int) -> int:
        root = x
        while self.par[root] != root:
            root = self.par[root]

        while x != root:
            p = self.par[x]
            self.par[x] = root
            x = p

        return root

    def union(self, a: int, b: int):
        a = self.find(a)
        b = self.find(b)

        if a == b:
            return

        if self.rank[a] < self.rank[b]:
            a, b = b, a

        self.par[b] = a

        if self.rank[a] == self.rank[b]:
            self.rank[a] += 1
```

# BFS / heapq / 二分探索は普通に使いやすい

## BFS

```python
from collections import deque

n = len(graph)
dist = [-1] * n
dist[0] = 0
q = deque([0])

while q:
    u = q.popleft()

    for v in graph[u]:
        if dist[v] != -1:
            continue

        dist[v] = dist[u] + 1
        q.append(v)
```

## heapq

```python
import heapq

pq = []
heapq.heappush(pq, 5)
heapq.heappush(pq, 2)

print(heapq.heappop(pq))
```

最大値を取りたいなら符号を反転します。

```python
pq = []
heapq.heappush(pq, -5)
heapq.heappush(pq, -2)

print(-heapq.heappop(pq))
```

任意要素削除が必要なら、削除済み要素を別に持つ遅延削除が便利です。

```python
from collections import Counter
import heapq

pq = []
deleted = Counter()


def push(x: int):
    heapq.heappush(pq, x)


def erase(x: int):
    deleted[x] += 1


def pop_min() -> int:
    while pq and deleted[pq[0]] > 0:
        deleted[pq[0]] -= 1
        heapq.heappop(pq)

    return heapq.heappop(pq)
```

## bisect

`bisect`は普通に使えます。

```python
from bisect import bisect_left

arr = [1, 2, 3, 5, 6, 7, 8, 9]
idx = bisect_left(arr, 8)
```

# Codonが強いところ

Codonの強みは、Pythonだと重くなりやすいループをそのまま高速に回しやすいことです。

例えば普通のDPも、変にベクトル化しようとせず素直に書けます。

```python
n = 1000
dp = [0] * (n + 1)

for i in range(n):
    for j in range(i + 1):
        # 遷移
        pass
```

Pythonでは「ループをどう減らすか」を実装上かなり意識しますが、Codonではアルゴリズム自体の計算量が間に合うなら、愚直なforループを書ける場面が増えます。

もちろん$O(N^3)$が何でも通るわけではないので、計算量そのものは普通に見積もる必要があります。

# SortedSetが欲しいとき

Python標準には平衡二分探索木ベースのSortedSetがありません。

競プロでは[tatyam-prime/SortedSet](https://github.com/tatyam-prime/SortedSet)系の平方分割実装をCodon向けに使う方法があります。

典型的には、

```python
x in s
s.add(x)
s.discard(x)
s.lt(x)
s.le(x)
s.gt(x)
s.ge(x)
s[i]
s.index(x)
```

などを$O(\sqrt N)$程度で処理できます。

セグ木と違い「値そのものを挿入・削除して順序付き集合として扱いたい」ときに便利です。

# NumPy / SciPyについて

AtCoderのCodon環境にはNumPyなどのPythonパッケージも入っていますが、**Codon自体がCPython + NumPy/SciPyと完全互換という意味ではありません**。

競プロの典型問題なら、NumPyへ寄せるよりCodonの普通のlistとforループで書く方が素直なことが多いです。

# PyPyへ戻した方がよい場面

Codonに固執しない方が速い問題もあります。

例えば、

- $2^{63}$を大きく超える整数を頻繁に扱う
- `Fraction`が本質的に必要
- Python標準ライブラリの未対応機能へ強く依存している
- `cmp_to_key`を使う複雑な比較ソートが中心
- CPython向けライブラリの挙動をそのまま期待したい

といった場合です。

Codonは「Pythonを完全再現するもの」ではなく、**Python風に競プロコードを書いて高速なネイティブコードへコンパイルする言語**として使うとかなり快適です。

# 提出前チェック

CodonでWA・RE・CEが出て、Pythonでは動くときは次を疑うとよいです。

1. 64bit整数がoverflowしていないか
2. 負数の`%`にPythonと同じ挙動を期待していないか
3. 再帰が深くなっていないか
4. `Fraction`、`math.isqrt`、`cmp_to_key`など未対応機能を使っていないか
5. `pow(a, -1, mod)`を使っていないか
6. `bool`と`int`を混ぜていないか
7. Pythonライブラリの完全互換を前提にしていないか
8. 型推論で詰まった箇所をリスト内包表記へ書き換えられないか

このあたりを見れば、Codon特有の事故はかなり減らせます。

## 参考

- [AtCoderで使用出来る言語とライブラリの一覧 - 2025/10](https://img.atcoder.jp/file/language-update/2025-10/language-list.html)
- [Codon Documentation](https://docs.exaloop.io/codon/)
- [Codon（AtCoder Language Update 2025 に際して） - いかたこのたこつぼ](https://ikatakos.com/pot/programming_algorithm/python_tips/codon)
- [tatyam-prime/SortedSet](https://github.com/tatyam-prime/SortedSet)
