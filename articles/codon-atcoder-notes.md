---
title: AtCoder テンプレ Codon
emoji: 📝
type: tech
topics:
- atcoder
- codon
- kyopro
published: true
---

```
# https://zenn.dev/yuubinnkyoku/articles/codon-atcoder-notes より
```
# 入出力・基礎機能

## 入出力、format関数

Codonは `f-string` が非常に高速で推奨されます。
入力は `input()` で受け取れますが、Codonの標準ライブラリレベルで高速化されているため、`sys.stdin.readline` を使う必要性はPythonほど高くありません。型注釈をつけるとコンパイル時に有利です。

```python
# 基本的な入力
n = int(input())
a, b = input().split() # 空白区切りの複数文字列
l = [_ for _ in input()] # 区切りなしの複数文字列→分割して入力

# 複数数値入力
l, m = list(map(int, input().split())) 
l, m = [int(_) for _ in input().split()] #内包表記でもいける

# 複数数値入力の繰り返し
# l1,r1
# l2,r2
# ..,..
# ln,rn
__=[[int(___) for ___ in input().split()] for _ in range(n)]; l = [___[0] for ___ in __]; r = [___[1] for ___ in __]

# リスト入力
c = [int(_) for _ in input().split()] # リスト内包表記推奨

# 二次元配列入力
h = 5
s = [[int(c) for c in input()] for _ in range(h)]

# 桁数を指定して出力・f-string
val_a = 100
val_b = 0.987654321
print(f'{val_a:06d}-{val_b:6f}')  # 000100-0.987654

# リスト出力 (1次元,空白区切り)
for i in l:print(i,end=" ")
print(" ".join(map(str, l))) #これでもいける

# リスト出力 (2次元,空白区切り)
for i in l:
	for j in i:
		print(j, end=" ")
	print()

# リスト出力（2次元、区切りなし）
for i in l:print("".join(i))
```
 ちなみに1e6はfloatになるので`10**6`と書こう
 intの上限は$2^{63}-1$で、`9.22*(10**8)`ぐらい
 オーバーフローしそうな時は`i128()`で全ての整数を囲む
## 初期化、内包表記

Codonではリストのメモリ確保が効率的ですが、Python同様に参照の扱いは注意が必要です。`copy` モジュールは使えますが、内包表記で新しく作ったほうがコンパイラにとって最適化しやすい場合が多いです。

```python
# 1次元配列初期化
a = [0] * 5
b = a[:] # スライスによるコピー

# 2次元配列初期化 (内包表記を使うのが鉄則)
h, w = 5, 3
dp = [[0] * w for _ in range(h)]

# 内包表記（CodonはこれがC++並に爆速になります）
odd = [i for i in range(100) if i % 2 == 1]

f=1 if k in a else 0 # k in aがTrueの時1、Falseのとき0
```
## 床関数、天井関数
```python
floor(x)
ceil(x)
```
## 添字と中身を同時に回す
リストでも文字列でも
インデックスと中身を同時に1文字で回せる
```python
for i, x in enumerate(a):
    # i: 添字
    # x: a[i]
```

## 二次元配列の転置、90°回転
```python
# 転置 (Transpose)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
transposed = [[row[i] for row in matrix] for i in range(w)]

# 90度回転など
rotated = [list(row)[::-1] for row in zip(*matrix)] # zip(*...)テクニックはCodonでも有効

# 8方向
hoko=[[-1,-1],[-1,0],[-1,1],[0,-1],[0,1],[1,-1],[1,0],[1,1]]

# 4方向
hoko=[[-1,0],[0,-1],[0,1],[1,0]]

# 周辺9マス
hoko=[[-1,-1],[-1,0],[-1,1],[0,-1],[0,0],[0,1],[1,-1],[1,0],[1,1]]
```
## setの削除、取り出し、リスト全部追加、包含判定
```python
s.remove() #エラーになる
s.discard()  #エラーにならない TrueFalseとかは返ってこない

x = next(iter(s)) #適当に1つ取り出す
x = s.pop() #適当に1つ取り出して削除

s.update({4, 5}) #リスト/集合で追加
s.difference_update([5, 6]) #リスト/集合で消去  エラーにならない
```

`set` の包含判定は `in` で1つずつ見るより、集合演算を使うと短く書けます。
```python
s1 = {1, 2}
s2 = {1, 2, 3}
print(s1 <= s2) # 部分集合
print(s1 < s2)  # 真部分集合
```
## dictのキーの所在判定/出現回数、文字列の合成

辞書にキーがあるかは `key in d` が基本です。`key in d.keys()` や `key in set(d.keys())` はCodonで遅くなったりコンパイルに詰まったりしやすいです。

```python
d = {1: 10, 2: 20}
if 1 in d:
    print(d[1])
```


こういうの
```python
  if a in d:d[a]+=1
  else:d[a]=1
```
は
```python
d[a] = d.get(a, 0) + 1
```
と書くことができる(勝手に追加されたりもしない)
`d.get(a, 0)`はaがあるときその個数、なければ0を返すという意味


文字列を `ans += ch` のように伸ばし続けると遅い。リストに貯めて最後に `join` する

```python
res = []
for ch in "abc":
    res.append(ch)
print("".join(res))
```

ちなみにリストは`[12,59,0]>[12,58,59]`みたいな大小比較もできるしソートも出来る
## dequeの追加、削除

```python
from collections import deque

d=deque(a) #listを渡せる O(n)

d.append(x) #末尾に追加 ならしO(1)
d.appendleft(x) #最初に追加 ならしO(1)

a=d.pop() #末尾を取得して削除 O(1)
a=d.popleft() #先頭を取得して削除 O(1)

d[2] #ランダムアクセス O(1) 代入やスライスは不可
```
~~`remove`、`.index`、`.count`、~~`in`、~~`reverse`、~~`reversed`、`len`なども使える 意外とCodonだと使えなかった

## Fraction（有理数）
Codonだと使えない。分母と分子を持って頑張るか諦めてPyPyなどを使おう

```python
from fractions import Fraction

a = Fraction(2, 6)   # 自動で約分され、1/3

#和差積商、n乗などは普通にできる

bunsi=a.numerator   # 分子: 1
bunbo=a.denominator # 分母: 3
print(float(a))     # 小数へ変換

#10進数の**文字列**から作る
Fraction("0.1")       # 1/10
Fraction("1.25")      # 5/4
Fraction("3/7")       # 3/7

# floatから直接作ると浮動小数点数誤差でバグるので注意！！！
Fraction(0.1)#バグる
Fraction(str(0.1))#絶対にこうする
```
## match文
```python
match a:
	case 1:
		print(1)
	case 2:
		print(2)
	case _: #else:みたいな感じ
		print("other")
```

```python
match n:
    case x if x < 0: #x<0であるようなxと等しいか?
        print("negative")
    case 0:
        print("zero")
    case _:
        print("positive")

#これと等価

if n < 0:
    print("negative")
elif n == 0:
    print("zero")
else:
    print("positive")
```
## 浅いコピー、深いコピー
中身がint, str, float, bool, intのtuple →浅いコピーでいい
中身がlist, dict→次元に応じた深いコピーが必要
新しいものを作る場合(a=10に+=1して「11」という新たなものを作るなど)は浅くても片方しか変わらない
新しいガワは作らずに中身だけ変わる場合は浅いと同期されちゃう

- 浅いコピー
```python
ndp = dp
```
  
- 深いコピー
```python
# 1次元配列
ndp = dp[:]
# 2次元配列
ndp = [row[:] for row in dp]
# 3次元配列
ndp = [[row[:] for row in mat] for mat in dp]

# これでも良いけど重い
from copy import deepcopy
b = deepcopy(a)
```

## bit演算
Codonの int はPythonの無限桁整数ではなく、基本は64bit符号付き整数。
そのため 1 << 100 みたいな巨大bit集合はPython感覚で使わない方がよい。
競プロなら n <= 60 程度のbit集合は扱いやすい。
bit演算は`+`とか`-`より優先順位が低いので注意

```python
x & y # AND
x | y # OR
x ^ y # XOR
~x # NOT

3 << 2 # 左シフト=2^k倍
12 >> 2 # 右シフト=2^kで割った床

x.popcnt() #2進数での各位の和
x.bit_length #2進数での桁数 floor(log2(x))+1と等しい(x>0) `64 - abs(self).__ctlz__()`じゃないと動かないかも?

# 代入付き演算子もできる
a &= b # a = a & b
a |= b # a = a | b
a ^= b # a = a ^ b
a <<= 2 # a = a << 2
a >>= 1 # a = a >> 1

# i bit目が立っているか見る
# 一番右が0bit目
x = 13 # 1101
i = 2
if (x >> i) & 1:
    print("i bit目は1")
else:
    print("i bit目は0")

# i bit目を立てる
x = 0
i = 3
x |= 1 << i
print(x) # 1000 -> 8

# i bit目を消す
x = 15 # 1111
i = 2
x &= ~(1 << i)
print(x) # 1011 -> 11

# i bit目を反転する
x = 15 # 1111
i = 1
x ^= 1 << i
print(x) # 1101 -> 13

# i bit目を j にする
x = (x & ~(1 << i)) | (j << i)

# 部分集合列挙
s = 13 # 1101
sub = s
while sub:
    print(sub)
    sub = (sub - 1) & s
# 1101, 1100, 1001, 1000, 0101, 0100, 0001
# S⊂T⊂{0,1,2,...,n-1}などの列挙で使う
# ちなみにこれはある数xが出てくるのが「SとT」「Sのみ」「なし」の3パターンあり、これがn個あるので組み合わせは3^n個ある

# 空集合も含める部分集合列挙
s = 13
sub = s
while True:
    print(sub)
    if sub == 0:
        break
    sub = (sub - 1) & s

# 昇順で空集合を含めない
s = 13 # 1101  
sub = 0  
while True:  
	sub = (sub - s) & s  
	if sub == 0:  
		break  
	print(sub)
```
s=101011の部分集合の中で
sub=101000の次に小さいのは
100011だが、これはsub-1=101000-1=100111とs=101011の和集合である

一応、10進数2進数変換
```python
bin(x) # 10進数 -> 2進数文字列  0bがつく
int(s, 2) # 2進数文字列 -> 10進数
```

## 対数関数
```python
import math

math.log(x, y) # log_y x
math.log10(x)
math.log2(x)
```
# 基礎アルゴリズム

## 2重ループ脱出、二分探索

`bisect` モジュールはCodonでも標準サポートされています。
ループ処理はCodonの得意分野（C++等と同等速度）なので、無理にライブラリを使わず愚直に書いても高速です。

```python

# 2重ループ脱出
# Codonでもフラグ管理や関数化してreturnで抜けるのがきれい
def solve_loop(n, y):
    for i in range(n + 1):
        for j in range(n - i + 1):
            k = n - i - j
            if y - 10000 * i - 5000 * j == 1000 * k:
                print(f'{i} {j} {k}')
                return
    print('-1 -1 -1')

# 二分探索
import bisect
arr = [1, 2, 3, 5, 6, 7, 8, 9]
idx = bisect.bisect_left(arr, 8)
```

- ライブラリなし二分探索
```python
left = 0
right = len(array) - 1
while left <= right:
  mid = (left + right) // 2
  if array[mid] == target:
    print(mid)
  elif array[mid] < target:
    low = mid + 1
  else:
    high = mid - 1
```
leftが指すところは絶対にFalse、rightが指すところは絶対にTrueのように意識するとバグらせにくい
doubleの二分探索は浮動小数点数誤差でバグりやすいので、forで十分な回数を指定してやると良い
あとCodonの場合出力時にf-stringで
```python
print(f"{mid:.15f}")
```
のようにしないと勝手に丸められる

## アルファベット一覧

`str.maketrans` / `translate` はCodonのバージョンによっては未実装または挙動が異なる可能性があるため、辞書を使った置換が無難です。

```python
# アルファベット
# ord('a') -> 97
# chr(97) -> 'a'
al = [chr(97 + i) for i in range(26)]
```
## 尺取り法
区間の更新を $O(1)$ でできれば、全体で $O(N)$ 
単調性があることが条件
片方をforで固定して、もう片方をwhileで1ずつ増やしていく感じ

- 区間和がK以下(すべて非負)の例
```python
def shakutori(a: list[int], k: int):
    n = len(a)
    l = 0
    s = 0
    cnt = 0
    length = 0
    for r in range(n):
        s += a[r]  # 右端を伸ばす
        while s > k:  # 条件を満たすまで左端を縮める
            s -= a[l]
            l += 1
        cnt += r - l + 1
        length = max(length, r - l + 1)
    return cnt, length


a = [2, 1, 3, 2, 1]
print(shakutori(a, 4))  # (8, 2)
```
`l`：区間の左端（含む）
`r`：区間の右端（含む）
`s`：現在の区間和
`cnt`：条件を満たす区間の総数
`length`：条件を満たす区間の最大長
## 座標圧縮（座圧）

```python
def zaatu(arr: list[int]) -> list[int]:
    """
    配列を座標圧縮して、圧縮後の配列を返す
    例: [100, 20, 20, 50] -> [2, 0, 0, 1]
    """
    # 1. 重複を除去してソート (O(N log N))
    unique_sorted = sorted(list(set(arr)))
    
    # 2. 値 -> 順位 のマッピングを作成 (O(N))
    mapping = {val: i for i, val in enumerate(unique_sorted)}
    
    # 3. 元の配列を変換 (O(N))
    return [mapping[x] for x in arr]

# --- 使用例 ---
def main():
    # 入力例
    A = [8, 100, 33, 33, 100, 12, 8]
    
    # 実行
    compressed_A = zaatu(A)
    
    print(f"Original:   {A}")
    print(f"Compressed: {compressed_A}")

    # 復元用のリスト（必要であれば）
    # unique_sorted があれば、 compressed_A[i] の元の値は unique_sorted[compressed_A[i]]

if __name__ == "__main__":
    main()
```

## bit全探索
```python
for i in range(2 ** n):
	for j in range(n):
		if ((i >> j) & 1):
			#n個のうちj番目が1である
```

- n&1とn%2は等価
  例 5&1→0b101&0b001→0b001
  1を2進数にしたときの$2^1$の位以降は0なので、$2^0$の位=偶奇がそのままのこる

## 平方数判定、正確な平方根、その床

平方根を `n ** 0.5` や `pow(n, 0.5)` の小数で扱うと、巨大な整数で誤差が出てWAになりやすいです。~~平方数判定や「平方根の床」が必要なときは `math.isqrt` を使います。~~ Codon未対応

```python
import math

n = int(input())
r = math.isqrt(n) #Codonだと使えない！！！

if r * r == n:
    print("square")
else:
    print("not square")

# floor(sqrt(n)) が欲しいだけなら r を使う
print(r)
```
# グラフ
- 木の性質
連結で閉路を持たない無向グラフ
木の点の数=辺の数+1
頂点数2以上の木には必ず次数1の点が存在する
次数1の点の中で1つを根として見ると、
- すべての辺は根に近づくような向きを持てる
- すべての点は、隣接する頂点の中でより根に近いもの=「親」を持てる
- 子を持たない頂点=葉が末端にある

二分木=各頂点が高々2個の子を持つ木
隣接行列
隣接リスト

単純=同じところを結ぶ複数の辺や再帰的な辺がない
## 入力
```python
n, m = [int(_) for _ in input().split()]
graph = [[] for _ in range(n)]

for i in range(m):
    a, b = [int(_) for _ in input().split()]
    a -= 1
    b -= 1
    graph[a].append(b)
    graph[b].append(a)

# 重み付き
n, m = [int(_) for _ in input().split()]
graph = [[] for _ in range(n)]

for i in range(m):
    a, b, c = [int(_) for _ in input().split()]
    a -= 1
    b -= 1
    graph[a].append((b, c))
    graph[b].append((a, c))

# 木なら辺はn-1個
n = int(input())
graph = [[] for _ in range(n)]

for i in range(n - 1):
    a, b = [int(_) for _ in input().split()]
    a -= 1
    b -= 1
    graph[a].append(b)
    graph[b].append(a)
```
## 深さ探索（DFS）
Codonはクラスや辞書の扱いも高速です。`deque` は `collections` からインポートします。

- DFS (Stack)
```python
n=7
graph=[]

def dfs_stack(start, n, graph):
    # 距離が必要なら dist = [-1] * n_nodes などにする
    visited = [False] * n
    stack = [start]
    visited[start] = True
    print(f"Visit: {start}") 

    while stack:
        u = stack.pop()
        for v in graph[u]:
            if not visited[v]:
                visited[v] = True
                stack.append(v)
                # --- ここで追加時の処理 ---
                print(f"Found: {v} (from {u})")

dfs_stack(0, n, graph)
```
- DFS(Stack)+経路復元
```python
def dfs_with_path(start_node, goal_node, n_nodes, graph):
    # 親ノードを記録する配列 (-1は未訪問/親なし)
    prev = [-1] * n_nodes
    prev[start_node] = start_node # スタート地点の親は自分自身などにしておく
    
    stack = [start_node]
    
    while stack:
        u = stack.pop()
        
        if u == goal_node:
            break
        
        for v in graph[u]:
            if prev[v] == -1: # 未訪問チェック
                prev[v] = u   # vの親はu
                stack.append(v)
    
    # 経路復元
    if prev[goal_node] == -1:
        return [] # 到達不能
        
    path = []
    curr = goal_node
    while curr != start_node:
        path.append(curr)
        curr = prev[curr]
    path.append(start_node)
    return path[::-1] # 逆順にして返す

# 使用例
path = dfs_with_path(0, 4, N, adj)
print(*path) # 出力例: 0 3 6 4 (順序は隣接リストの並び順依存)
```
- DFS(再帰)
```python
n = 7
graph = [[1, 2, 3],[0],[5, 0],[6, 0],[6],[2],[3, 4]]

visited = [False] * n

def dfs(u):
    visited[u] = True

    # 行きがけの処理
    print(f"Visit: {u}")

    for v in graph[u]:
        if not visited[v]:
            dfs(v)

    # 帰りがけの処理をするならここ

dfs(0)
```
- DFS(サイクル検出・復元) 無向グラフ
```python
# 無向グラフのサイクル検出・復元
# 戻り値: サイクルの頂点列
# なければ []

def find_cycle_undirected(n, graph):
    # 0: 未訪問
    # 1: 探索中
    # 2: 探索終了
    state = [0] * n
    parent = [-1] * n

    cycle_start = -1
    cycle_end = -1

    def dfs(u, p):
        nonlocal cycle_start, cycle_end

        state[u] = 1

        for v in graph[u]:
            if v == p:
                continue

            if state[v] == 0:
                parent[v] = u

                if dfs(v, u):
                    return True

            elif state[v] == 1:
                # 探索中の頂点に戻った → サイクル
                cycle_start = v
                cycle_end = u
                return True

        state[u] = 2
        return False

    # 非連結グラフにも対応
    for s in range(n):
        if state[s] == 0:
            if dfs(s, -1):
                break

    if cycle_start == -1:
        return []

    # cycle_start <- ... <- cycle_end をparentで復元
    cycle = [cycle_start]

    cur = cycle_end
    while cur != cycle_start:
        cycle.append(cur)
        cur = parent[cur]

    cycle.append(cycle_start)

    # 順方向にする
    cycle.reverse()

    return cycle
```

  - DFS(サイクル検出・復元) 有向グラフ

```python
# 有向グラフのサイクル検出・復元
# 戻り値: サイクルの頂点列
# なければ []

def find_cycle_directed(n, graph):
    state = [0] * n
    parent = [-1] * n

    cycle_start = -1
    cycle_end = -1

    def dfs(u):
        nonlocal cycle_start, cycle_end

        state[u] = 1

        for v in graph[u]:

            if state[v] == 0:
                parent[v] = u

                if dfs(v):
                    return True

            elif state[v] == 1:
                # u -> v で探索中の頂点に戻った
                cycle_start = v
                cycle_end = u
                return True

        state[u] = 2
        return False

    # 非連結でも全頂点を見る
    for s in range(n):
        if state[s] == 0:
            if dfs(s):
                break

    if cycle_start == -1:
        return []

    cycle = [cycle_start]

    cur = cycle_end
    while cur != cycle_start:
        cycle.append(cur)
        cur = parent[cur]

    cycle.append(cycle_start)
    cycle.reverse()

    return cycle
```
## 幅探索（BFS）

重みがないとき

- BFS
```python
from collections import deque

# BFS (幅優先探索)
n = 7 #頂点の数
graph = [[1, 2, 3], [0], [5, 0], [6, 0], [6], [2], [3, 4]] #ぐらふ

def bfs(start_node):
    dist = [-1] * n #各頂点までの最短距離
    dist[start_node] = 0
    q = deque([start_node])
    
    while q:
        u = q.popleft()
        for v in graph[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                q.append(v)
    return dist

print(bfs(0))
```

- グリッド多視点BFS
グリッド最短距離は `deque` でBFSします。スタートが複数ある場合は、最初に全部キューへ入れるだけです。

```python
from collections import deque

h, w = map(int, input().split())
l = [input() for _ in range(h)]
dist = [[-1] * w for _ in range(h)]
q = deque()

# 多始点の場合: 条件を満たすマスを全部入れる
for i in range(h):
    for j in range(w):
        if l[i][j] == "S":
            dist[i][j] = 0
            q.append((i, j))

dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]
while q:
    i, j = q.popleft()
    for di, dj in dirs:
        ni = i + di
        nj = j + dj
        if not (0 <= ni < h and 0 <= nj < w):
            continue
        if l[ni][nj] == "#":
            continue
        if dist[ni][nj] != -1:
            continue
        dist[ni][nj] = dist[i][j] + 1
        q.append((ni, nj))
```

- 経路復元
経路復元が必要な場合は、どこから来たかを `prev` に保存します。
```python
prev = [[(-1, -1)] * W for _ in range(H)]

# 遷移時
prev[ni][nj] = (i, j)

# goal から start へ戻して反転
path = []
cur = goal
while cur != start:
    path.append(cur)
    ci, cj = cur
    cur = prev[ci][cj]
path.append(start)
path.reverse()
```
## Bellman-Ford
計算量：頂点数N,辺数MならO(NM)
求まるもの：1つの始点から全頂点への最短距離
負の辺：OK
```python
INF = 1 << 60

N, M, S = map(int, input().split())

edges: list[tuple[int, int, int]] = []
for _ in range(M):
    u, v, w = map(int, input().split())
    edges.append((u, v, w))

dist = [INF] * N
dist[S] = 0

# 「使える辺の本数」を1本ずつ増やしていくイメージ
for _ in range(N - 1):
    updated = False

    for u, v, w in edges:
        # uまで行けるなら、
        # 「uまでの最短経路 + 辺 u->v」で v を更新
        if dist[u] != INF and dist[v] > dist[u] + w:
            dist[v] = dist[u] + w
            updated = True

    if not updated:
        break
```

負閉路判定も付けるなら、その後もう1回：

```python
negative_cycle = False

for u, v, w in edges:
    if dist[u] != INF and dist[v] > dist[u] + w:
        negative_cycle = True
```

## Floyd-Warshall
計算量：頂点数NならO(N^3)
求まるもの：全頂点同士の最短距離
負の辺：OK
```python
INF = 1 << 60

N, M = map(int, input().split())

dist = [[INF] * N for _ in range(N)]

for i in range(N):
    dist[i][i] = 0

for _ in range(M):
    u, v, w = map(int, input().split())
    dist[u][v] = min(dist[u][v], w)

# 中継地点として使ってよい頂点を
# 0, 1, 2, ... と1個ずつ増やす
for k in range(N):
    for i in range(N):
        for j in range(N):
            if dist[i][k] != INF and dist[k][j] != INF:
                dist[i][j] = min(dist[i][j],dist[i][k] + dist[k][j])
```
負閉路判定
```python
for i in range(N):
    if dist[i][i] < 0:
        # 負閉路あり
```
## Dijkstra法
計算量：頂点数N,辺数MならO((N+M)logN)
求まるもの：1つの始点から全頂点への最短距離
負の辺：ダメ！
```python
import heapq

INF = 10**18

dist = [INF] * n
dist[start] = 0
pq = [(0, start)]

while pq:
	d, u = heapq.heappop(pq) #コスト、頂点
	
	if d > dist[u]:
		continue
	
	for v, weight in graph[u]:
		if dist[u] + weight < dist[v]:
			dist[v] = dist[u] + weight
			heapq.heappush(pq, (dist[v], v))
```

## Union Find（DSU）
`find(x)`：償却 $O(\alpha(N))$
`union(x, y)`：償却 $O(\alpha(N))$
`is_same(x, y)`：償却 $O(\alpha(N))$
初期化 ：$O(N)$

$\alpha(N)$ は逆アッカーマン関数。増え方が異常に遅く、$N=10^{100}$ でも4なので、ほぼ$O(1)$

大きい木の根に小さい木の根をくっつける(マージテク)・長いやつは1つずつ直接根に付け替える(経路解析)で超高速になる

集合の結合と代表値の取得、同じ集合に含まれているか判定などができる
```python
class UnionFind:
    par: list[int]
    size: list[int]

    def __init__(self, n: int):
        self.par = [i for i in range(n)] #親
        self.size = [1] * n

    def find(self, x: int) -> int:
        root = x
        while self.par[root] != root:
            root = self.par[root]
        
        curr = x
        while curr != root:
            parent = self.par[curr]
            self.par[curr] = root
            curr = parent
        return root

    def union(self, x: int, y: int) -> bool:
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x == root_y:
            return False

        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x

        self.par[root_y] = root_x
        self.size[root_x] += self.size[root_y]

        return True

    def is_same(self, x: int, y: int) -> bool:
        return self.find(x) == self.find(y)

    def get_size(self, x: int) -> int:
        return self.size[self.find(x)]
    
    def groups(self) -> list[list[int]]:
	    groups = [[] for _ in range(len(self.par))]
	
	    for i in range(len(self.par)):
	        groups[self.find(i)].append(i)
	
	    return [group for group in groups if group]



uf = UnionFind(n)
uf.union(u, v)
uf.is_same(0, 4)
```

## Kruskal法（最小全域木）
頂点数N、辺数Mで
計算量 O(M log M)
コストが小さい辺から確定させ、サイクルは飛ばす
```python
class UnionFind:
    par: list[int]
    size: list[int]

    def __init__(self, n: int):
        self.par = [i for i in range(n)]
        self.size = [1] * n

    def find(self, x: int) -> int:
        root = x
        while self.par[root] != root:
            root = self.par[root]

        curr = x
        while curr != root:
            parent = self.par[curr]
            self.par[curr] = root
            curr = parent

        return root

    def union(self, x: int, y: int) -> bool:
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x == root_y:
            return False

        if self.size[root_x] < self.size[root_y]:
            root_x, root_y = root_y, root_x

        self.par[root_y] = root_x
        self.size[root_x] += self.size[root_y]

        return True

    def is_same(self, x: int, y: int) -> bool:
        return self.find(x) == self.find(y)

    def get_size(self, x: int) -> int:
        return self.size[self.find(x)]
    
    def groups(self) -> list[list[int]]:
	    groups = [[] for _ in range(len(self.par))]
	
	    for i in range(len(self.par)):
	        groups[self.find(i)].append(i)
	
	    return [group for group in groups if group]


n, m = [int(_) for _ in input().split()]

edges = []

for _ in range(m):
    u, v, cost = [int(_) for _ in input().split()]
    edges.append((cost, u - 1, v - 1))

edges.sort()

uf = UnionFind(n)

ans = 0

for cost, u, v in edges:
    if uf.union(u, v):
        ans += cost

print(ans)
```
## LCA（最小共通祖先）
木上で、2頂点 `u, v` の最小共通祖先を高速に求めます。  
ダブリングを使う
前処理 `O(N log N)`、各クエリ `O(log N)` 
- LCA(n, graph, 0): 頂点数nで根が0の木graphをつくる
-  lca(u, v): u と v の最小共通祖先
- dist(u, v): u と v の木上距離
- kth_ancestor(v, k): v から k 個上の祖先

```python
class LCA:
    n: int
    log: int
    depth: list[int]
    parent: list[list[int]]

    def __init__(self, n: int, graph: list[list[int]], root: int):
        self.n = n

        # log = ceil(log2(n)) + 1 相当
        self.log = 1
        while (1 << self.log) <= n:
            self.log += 1

        self.depth = [-1] * n
        self.parent = [[0] * n for _ in range(self.log)]

        # 再帰DFSではなくスタックで根付き木を作る
        stack = [root]
        self.depth[root] = 0
        self.parent[0][root] = root

        while stack:
            u = stack.pop()
            for v in graph[u]:
                if self.depth[v] != -1:
                    continue
                self.depth[v] = self.depth[u] + 1
                self.parent[0][v] = u
                stack.append(v)

        # ダブリング表を作る
        for k in range(self.log - 1):
            for v in range(n):
                self.parent[k + 1][v] = self.parent[k][self.parent[k][v]]

    def kth_ancestor(self, v: int, k: int) -> int:
        # v から k 個上の祖先を返す
        for i in range(self.log):
            if (k >> i) & 1:
                v = self.parent[i][v]
        return v

    def lca(self, a: int, b: int) -> int:
        # 深さをそろえる
        if self.depth[a] < self.depth[b]:
            a, b = b, a

        diff = self.depth[a] - self.depth[b]
        a = self.kth_ancestor(a, diff)

        if a == b:
            return a

        # 上から見て、親が違うところまで同時に上げる
        for k in range(self.log - 1, -1, -1):
            if self.parent[k][a] != self.parent[k][b]:
                a = self.parent[k][a]
                b = self.parent[k][b]

        return self.parent[0][a]

    def dist(self, a: int, b: int) -> int:
        c = self.lca(a, b)
        return self.depth[a] + self.depth[b] - 2 * self.depth[c]


lca = LCA(n, graph, 0)

print(lca.lca(4, 5))  
print(lca.dist(4, 5))
```
## 超頂点
複数の頂点を「同じグループ」としてまとめて扱いたいとき、グループそのものを表す頂点(超頂点)を追加する。
グループ内の頂点を互いにすべてつなぐと $O(n^2)$ 本の辺が必要だが、超頂点を経由することで $O(n)$ 本にできる。

元の頂点を `0 ~ n-1`、グループ`i`を表す超頂点=`n+i`として`n`~`n+m-1` に置く。

- コスト`c`で移動する場合
```python
# graph[u] = [(行き先, コスト), ...]

graph = [[] for _ in range(n + m)]

for i in range(m):
    super_node = n + i
    c = cost[i]
    for v in groups[i]:
        graph[v].append((super_node, 0))
        graph[super_node].append((v, c))
```
このように、`u -> 超頂点` か `超頂点 -> v` のどちらかで`c`を加算する

# 数学・数え上げ
## ソート
```python
# ソート
w = [(1, 2), (2, 6), (3, 6), (4, 5), (5, 7)]
w.sort()
w.sort(key=lambda x: x[1], reverse=True) #2つ目の数で降順ソート
```
別のリストを連動させたい場合は一度上のようにtupleのlistにしてからソートするなど

- バブルソート
…swapし続けるやつ
転倒数回だけswapすると必ずソートできる（1回swapすると必ず1つ減るので）
転倒数は、最大で$O(n^2)$
whileだと転倒している場所を見つけるのに$O(n)$かかる
なので計算量は$O(n^3)$
とはいえ、1周するごとに1番後ろが確定するのでn周比較し続ければ十分。よって$O(n^2)$でできる

- 2つの値を比較する関数を指定
functools.cmp_to_keyはCodonでは使えないので、マージソートを温もりの手実装するかPyPyを使おう
```python
from functools import cmp_to_key

def cmp(x, y):  
	if x < y:  
		return -1 #xが先
	if x > y:  
		return 1 #yが先
	return 0 #等価

l.sort(key=cmp_to_key(cmp))
```
ちなみにx,yにはそれぞれlの1つの要素が来るので、2次元リスト的なやつとかまで割といい感じに処理できる

## エラトステネスの篩（素数列挙）、素因数分解

エラトステネスの篩→$O(n \log \log n)$
```python
# 素数判定/エラトステネスの篩
# 大きめの数を大量に素因数分解するならこれで事前計算
n_prime = 100
is_prime = [True] * (n_prime + 1)
is_prime[0] = False
is_prime[1] = False
for i in range(2, int(n_prime**0.5) + 1):
    if is_prime[i]:
        for j in range(i * 2, n_prime + 1, i):
            is_prime[j] = False

primes = [i for i, p in enumerate(is_prime) if p]


# 素因数分解 O(√n)​ 10^12以下ぐらいの数を数回ぐらいならこっち
def prime_factorize(n: int):
    pf = {}
    while n % 2 == 0:
        pf[2] = pf.get(2, 0) + 1
        n //= 2
    d = 3
    while d * d <= n:
        while n % d == 0:
            pf[d] = pf.get(d, 0) + 1
            n //= d
        d += 2
    if n > 1:
        pf[n] = 1
    return pf

print(prime_factorize(341555136))
```

```python
# 10^7以下ぐらいの数を大量に素因数分解する場合はこっち
# SPF: Smallest Prime Factor (0 ~ n がもつ最小の素因数をすべて事前計算) O(NloglogN)
def make_spf(n: int) -> list[int]:
    spf = [0] * (n + 1)
    if n >= 1:spf[1] = 1
    for p in range(2, n + 1):
        if spf[p] != 0:continue
        spf[p] = p
        if p * p <= n:
            for x in range(p * p, n + 1, p):
                if spf[x] == 0:spf[x] = p
    return spf
# O(logN)
def prime_factorize(m: int, spf: list[int]):
    pf = {}  # Dict[int, int]
    temp = m
    while temp > 1:
        p = spf[temp]
        pf[p] = pf.get(p, 0) + 1
        temp //= p
    return pf
```
- Miller–Rabin素数判定 + Brent版Pollard Rho
[Miller–Rabin素数判定 + Brent版Pollard Rho 素因数分解](https://zenn.dev/yuubinnkyoku/articles/miller-rabin)
小さい方の素因数を `p` とすると、平均 `O(√p)`
$10^{18}$ぐらいの馬鹿でかい数用
## 最大公約数（GCD）、itertools、Counter、n進数変換

Codonでは `math.gcd` が使えます（fractionsは非推奨）。
`itertools` もサポートされていますが、順列などは再帰関数やforループで書いても十分高速です。

```python
# GCD / LCM
import math
a, b = 12, 18
f = math.gcd(a, b)
f2 = (a * b) // f
print(f'{f} {f2}')
# 3つ以上もいける
f = math.gcd(a,b,c)
f = math.gcd(*l) #これはCodonだと使えないので2つずつforを回すなど
f = math.lcm(*l) #これも同様
def gcd4list(l):  
	g = 0
	for x in l:  
		g = math.gcd(g, x)  
	return g
def lcm4list(l):  
	res = 1  
	for x in l:  
		res = math.lcm(res, x)  
	return res
```

```python
# itertools (順列、組み合わせ、重複組み合わせ、直積、累積和)
# 中身tuple
# Codonのイテレータはリスト化しないといろいろバグりそうなのでlistにしておくと良い

from itertools import permutations
# 同じ文字でも別物として扱うのでlist(sorted(set()))にすればいい感じになる
zyunretu=list(permutations(a)) #並べ替える
zyunretu2=list(permutations(a, 2)) #2つ選んで並べる

from itertools import combinations
kumiawase2=list(combinations(a, 2)) #2つ選ぶ

from itertools import combinations_with_replacement
tyouhuku_kumiawase2=list(combinations_with_replacement(a, 2)) #同じ文字の組み合わせありで2つ選ぶ

from itertools import product
tyokuseki=list(product([0, 1], repeat=3)) # [(0, 0, 0), (0, 0, 1), ..., (1, 1, 1)]

from itertools import accumulate
ruiseki=list(accumulate(l)) #累積和も一応できる ruiseki[0]は0ではなく、l[0]からはじまる


# Counter
from collections import Counter
vals = [2,2,2,3,4,3,1,2,1,3,1,2,1,2,2,1,2,1]
cnt = Counter(vals)
# most_common も使用可能
for k, v in cnt.most_common(3):
    print(f'{k}: {v}')
    
# n進数変換
def base_n(num: int, k: int) -> str:
    if num == 0: return '0'
    res = ''
    while num != 0:
        rem = num % abs(k)
        if k < 0:
            num = -(-num // k) # ceil division logic for negative base
        else:
            num //= k
        res += str(rem)
    return res[::-1]

bin(x) # 10進数 -> 2進数文字列  
int(s, 2) # 2進数文字列 -> 10進数
```
## modの割り算、modつきコンビネーション

- 割り算
割り算→逆元をかけるだけ
逆元が存在する必要十分条件は、`gcd(a,MOD)==1`
```python
#pow(y,-1,mod) Codonでは動かない
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
modが素数で`y % MOD != 0`の時はこれでもいい(フェルマーの小定理)
```python
pow(a, MOD - 2, MOD)
```

また、mとyが正, $\frac{x}{y}\in\mathbb{Z}$であることが分かっている場合、
$$
\boxed{
\left(\frac{x}{y}\right)\bmod m
=
\frac{x\bmod(ym)}{y}
}
$$

- コンビネーション(二項係数)
`pow(n, r, mod) = nのr乗のmodで割った余り` は速い
$$
{}_n\mathrm{C}_r
\equiv
n!(r!)^{-1}\bigl((n-r)!\bigr)^{-1}
\pmod{10^9+7}
$$
を利用して、以下のようにO(MAX)+O(1)で計算できる

```python
MOD = 10**9 + 7

def inverse(n: int) -> int: #逆元
    return pow(n, MOD - 2, MOD)

MAX = 200000
fact = [1] * (MAX + 1)
inv = [1] * (MAX + 1)
for i in range(2, MAX + 1):
    fact[i] = (fact[i - 1] * i) % MOD

inv[MAX] = inverse(fact[MAX])
for i in range(MAX - 1, 1, -1):
    inv[i] = (inv[i + 1] * (i + 1)) % MOD

def nCr(n: int, r: int) -> int:
    if n < r or n < 0 or r < 0: return 0
    num = fact[n]
    den = (inv[r] * inv[n - r]) % MOD
    return (num * den) % MOD

print(nCr_mod(100000, 50000))
```
rが十分に小さく、繰り返し計算しない時はこれでO(min(r,n−r))
```python
MOD = 10**9 + 7

def nCr(n: int, r: int) -> int:
    if n < 0 or r < 0 or r > n:
        return 0
    r = min(r, n - r)
    numerator = 1
    denominator = 1
    for i in range(1, r + 1):
        numerator = numerator * (n - i + 1) % MOD
        denominator = denominator * i % MOD
    return numerator * pow(denominator, MOD - 2, MOD) % MOD

print(nCr(100000, 2))
```
$p$ が素数、$a$ は $p$ で割り切れない整数のとき、
$$
a^{p-1}\equiv 1 \pmod p  
$$
である。(フェルマーの小定理)
なので両辺を$a$で割るとモジュラ逆数は$a^{p-2} \pmod p$であり、これは$O(\log p)$で求められる。

また、$1^{-1},2^{-1},...,(a-1)^{-1} \pmod p$が求まっているとき、以下のようにして$a^{-1}$は$O(1)$で求められる。
$x\left\lfloor \frac{p}{x} \right\rfloor = p - p \% x$より
$$
\begin{aligned}
x\left\lfloor \frac{p}{x} \right\rfloor
&\equiv -p \% x \\[4pt]
x\left\lfloor \frac{p}{x} \right\rfloor x^{-1}(p \% x)^{-1}
&\equiv -p \% x \, x^{-1}(p \% x)^{-1} \\
&\equiv -x^{-1} \pmod p
\end{aligned}
$$
よって
$$
x^{-1}
\equiv
-
x\left\lfloor \frac{p}{x} \right\rfloor x^{-1}(p \% x)^{-1} \pmod p
$$
これはp%aがあれば$O(1)$で求めることができる。
## Floor Sum
このような形の総和を$O(\log \max(a,m))$で求める
$$  
\sum_{i=0}^{n-1}  
\left\lfloor\frac{ai+b}{m}\right\rfloor  
$$
これは直線 $y=\frac{ax+b}{m}$ の下側のx軸との間にある格子点を、x=0~n-1まで数えているのと同じ

$n\geq0$, $m\geq1$, $a,b\geq0$のもとで、
```python
def floor_sum(n: int, m: int, a: int, b: int) -> int:
    """
    sum(floor((a*i+b)/m) for i in range(n))

    n >= 0
    m >= 1
    a, b >= 0

    O(log(max(a, m)))
    """
    ans = 0

    while True:
        # aに含まれるmの倍数部分
        if a >= m:
            ans += n * (n - 1) // 2 * (a // m)
            a %= m

        # bに含まれるmの倍数部分
        if b >= m:
            ans += n * (b // m)
            b %= m

        # ここからは 0 <= a,b < m
        y_max = a * n + b

        if y_max < m:
            break

        # 残った格子領域を縦横入れ替えて数える
        n = y_max // m
        b = y_max % m
        a, m = m, a

    return ans
```

# 累積和・動的計画法

## 累積和、動的計画法（DP）

Codonの真骨頂です。PythonだとTLEするような3重ループDPでも、CodonならC++同様に通ることが多いです。

```python
# 累積和
arr = list(range(1, 30))
cum = [0]
for x in arr:
    cum.append(cum[-1] + x)

# DP (ナップサック問題)
# 型を意識する: dpテーブルは int なのか float なのか統一しておくと良い
N_item = 6
W_limit = 8
weights = [2, 1, 3, 2, 1, 5]
values = [3, 2, 6, 1, 3, 85]

# Codonではリスト確保のコストが低いので、これでも十分高速
dp = [[0] * (W_limit + 1) for _ in range(N_item + 1)]

for i in range(N_item):
    for j in range(W_limit + 1):
        if j >= weights[i]:
            dp[i+1][j] = max(dp[i][j - weights[i]] + values[i], dp[i][j])
        else:
            dp[i+1][j] = dp[i][j]

print(dp[N_item][W_limit])
```
## メモ化再帰
- PyPy
```python
from functools import cache
import sys
sys.setrecursionlimit(10**6)

@cache
def fib(n):
    if n <= 1:
        return n
    return fib(n-1) + fib(n-2)

print(fib(10))
```
`@cache`は自動的に辞書にメモ化してくれるが、Codonでは使えない

- Codon
深さ制限がない
```python
memo: dict[tuple[int, int, int], int] = {}

def dp(a: int, b: int, t: int) -> int:
    key = (a, b, t)

    if key in memo:
        return memo[key]

    # 終了条件
    # if ...:
    #     return ...

    res = 0

    # 状態遷移
    # res += dp(...)

    memo[key] = res
    return res
```
## 累積min/max
右端・左端からの区間について、最小値・最大値を高速に取得できる。
任意の区間 `[l, r)` を指定することはできないのでスパーステーブルや値を更新したい場合はセグ木で代替できる。
前処理 $O(N)$、問い合わせ $O(1)$、空間計算量 $O(N)$
```python
class Acc:
    def __init__(self, a, f, unit):
        n = len(a)
        self.n = n
        self.left = [unit] * (n + 1)
        self.right = [unit] * (n + 1)
        for i in range(n):self.left[i + 1] = f(self.left[i], a[i])
        for i in range(n - 1, -1, -1):self.right[i] = f(a[i], self.right[i + 1])
    def __getitem__(self, s):
        if s.step is not None:raise ValueError("step is not supported")
        if s.start is None:
			r = self.n if s.stop is None else max(0, min(s.stop, self.n))
            return self.left[r]
        if s.stop is not None:raise ValueError("only [:r] or [l:] is supported")
        l = max(0, min(s.start, self.n))
        return self.right[l]


a = [3, 2, 5, 1, 4]

amin = Acc(a, min, 10**18)
amax = Acc(a, max, -(10**18))

print(amin[:3])  # 2
print(amin[2:])  # 1
print(amin[:])   # 1

print(amax[:3])  # 5
print(amax[2:])  # 5
print(amax[:])   # 5
```

`unit` は単位元。`min` なら十分大きな値、`max` なら十分小さな値を指定する。
`f` は結合律を満たす二項演算であればよく、`min`・`max` のほか、`math.gcd` なども利用できる。
## いもす法、2次元いもす法

区間加算を毎回全マスに反映するとTLEする場合、差分配列に端だけ記録して最後に累積和を取ります。

```python
# 1次元いもす: [l, r) に x を足す
n, q = map(int, input().split())
diff = [0] * (n + 1)
for _ in range(q):
    l, r, x = map(int, input().split())
    diff[l] += x
    diff[r] -= x

a = [0] * n
cur = 0
for i in range(n):
    cur += diff[i]
    a[i] = cur
```

```python
# 2次元いもす: [r1, r2) x [c1, c2) に x を足す
h, w, q = map(int, input().split())
diff = [[0] * (w + 1) for _ in range(h + 1)]
for _ in range(q):
    r1, c1, r2, c2, x = map(int, input().split())
    diff[r1][c1] += x
    diff[r2][c1] -= x
    diff[r1][c2] -= x
    diff[r2][c2] += x

for i in range(h):
    for j in range(w):
        diff[i][j + 1] += diff[i][j]
for j in range(w):
    for i in range(h):
        diff[i + 1][j] += diff[i][j]

ans = [[diff[i][j] for j in range(w)] for i in range(h)]
```
## 最長増加部分列（LIS）

DPで解ける
```python
from bisect import bisect_left

def length_of_lis(nums: list[int]) -> int:
    # 増加部分列の末尾の値を保持するリスト
    # tails[i] は、長さ i+1 の増加部分列の中で最小の末尾の値
    tails = []

    for x in nums:
        # x を挿入できる場所（x以上の最初の要素の位置）を探す
        idx = bisect_left(tails, x)

        if idx < len(tails):
            # 既存の値をより小さい値 x に更新することで、
            # 将来より長い列を作れる可能性を高める
            tails[idx] = x
        else:
            # x がこれまでのどの末尾よりも大きい場合、列を延長できる
            tails.append(x)

    return len(tails)

def main():
    # テストデータ
    nums = [10, 9, 2, 5, 3, 7, 101, 18]
    
    result = length_of_lis(nums)
    print(f"配列: {nums}")
    print(f"最長増加部分列の長さ: {result}")

if __name__ == "__main__":
    main()
```


# データ構造・文字列

## ランレングス圧縮（RLE）

連続する同じ文字・同じ大小関係・同じ状態をまとめる手法です。`<<<>>>` のような符号列、`00011100` のような文字列、連続区間ごとの処理でよく使います。

- 文字列の圧縮

```python
def rle(s: str) -> list[tuple[str, int]]:
    n = len(s)
    res = []
    l = 0
    while l < n:
        r = l + 1
        while r < n and s[l] == s[r]:
            r += 1
        res.append((s[l], r - l))
        l = r
    return res

s = input()
print(rle(s))
```

- リストの圧縮

```python
def rle_list(a: list[int]) -> list[tuple[int, int]]:
    res = []
    l = 0
    n = len(a)
    while l < n:
        r = l + 1
        while r < n and a[l] == a[r]:
            r += 1
        res.append((a[l], r - l))
        l = r
    return res
```
## Manacher（回文判定）
文字列中の各位置を中心とする最長回文をそれぞれ求める $O(N)$
- `odd[i]`：`s[i]` を中心とする回文の個数、最長の半径(中心も含む)
長さは `2 * odd[i] - 1`
- `even[i]`：`s[i-1]` と `s[i]` を中心とする回文の個数、最長の半径(中心も含む)
長さは `2 * even[i]`
```python
def manacher(s: str) -> tuple[list[int], list[int]]:
    n = len(s)
    odd = [0] * n
    left = 0
    right = -1
    for i in range(n):
        radius = 1
        if i <= right:
            mirror = left + right - i
            radius = min(odd[mirror], right - i + 1)
        while (
            i - radius >= 0
            and i + radius < n
            and s[i - radius] == s[i + radius]
        ):
            radius += 1
        odd[i] = radius
        if i + radius - 1 > right:
            left = i - radius + 1
            right = i + radius - 1

    even = [0] * n
    left = 0
    right = -1
    for i in range(n):
        radius = 0
        if i <= right:
            mirror = left + right - i + 1
            radius = min(even[mirror], right - i + 1)
        while (
            i - radius - 1 >= 0
            and i + radius < n
            and s[i - radius - 1] == s[i + radius]
        ):
            radius += 1
        even[i] = radius
        if i + radius - 1 > right:
            left = i - radius
            right = i + radius - 1

    return odd, even
```

- 区間が回文か判定
 `s[l:r]` が回文かを $O(1)$ で判定
```python
def is_palindrome(l: int, r: int, odd: list[int], even: list[int]) -> bool:
    length = r - l
    if length == 0:
        return True
    center = (l + r) // 2

    if length % 2 == 1: return odd[center] >= length // 2 + 1 #長さが奇数のとき
    return even[center] >= length // 2 #長さが偶数のとき
```

- 回文になる部分文字列(連続する方)の個数
```python
palindrome_count = sum(odd) + sum(even)
```
## 優先度付きキュー（heapq）

`deque` は入れた順番、`heapq` は値の大小順で取り出すためのものです。最小値を何度も取り出す問題で使います。最大値を取りたいときは-1倍する。
pushもpopも$O(\log{N})$

```python
import heapq

pq = []
heapq.heappush(pq, 5)
heapq.heappush(pq, 2)
heapq.heappush(pq, 8)

print(heapq.heappop(pq)) # 2
pq[0] #削除したくない場合
# 0は最小値だがそこ以外はソートされていないので注意！

# 最大値を取りたい場合
max_pq = []
heapq.heappush(max_pq, -5)
heapq.heappush(max_pq, -2)
print(-heapq.heappop(max_pq)) # 5
```

削除クエリがあるとき、ヒープ内の任意要素削除は遅いです。削除済みフラグやCounterを別に持って、取り出すときに無効な要素を捨てる「遅延削除」を使います。

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

## Next配列

文字列で「位置 `i` 以降に最初に出てくる文字 `c` の位置」を何度も聞きたいときに使います。部分列判定や、開始位置を全探索して次の文字へ飛ぶ問題で便利です。

```python
s = input()
n = len(s)
K = 26
INF = n
nxt = [[INF] * K for _ in range(n + 1)]

for c in range(K):
    nxt[n][c] = INF

for i in range(n - 1, -1, -1):
    for c in range(K):
        nxt[i][c] = nxt[i + 1][c]
    nxt[i][ord(s[i]) - 97] = i

# i以降の最初の'a'
pos = nxt[0][0]
```

## 括弧列、stack

括弧列判定…左から `(`→ +1、`)`→ -1 する。途中で負にならず最後が0ならOK
対応関係や削除処理が必要なら stack を使うと実装が楽

```python
s = input()
bal = 0
ok = True
for ch in s:
    if ch == "(":
        bal += 1
    else:
        bal -= 1
    if bal < 0:
        ok = False

print("Yes" if ok and bal == 0 else "No")
```

```python
# 対応する括弧の位置を取る
s = input()
stack = []
pairs = []
for i, ch in enumerate(s):
    if ch == "(":
        stack.append(i)
    elif ch == ")":
        if not stack:
            continue
        j = stack.pop()
        pairs.append((j, i))
```
## SortedSet

セグ木とは違い、挿入・削除できるが範囲の〇〇には弱い
長いので
SortedSet(Codon) Code
↑Ctrl押しながらでプレビュー、コピーできる

ほとんどの操作が $O(\sqrt N)$ である

```python
s = SortedSet(a=[]) # つくる,ソート済みならO(n)/されていなければO(n log n)

print( s.a ) #中身,listのlist(昇順に並んでいる)
len(s) # 長さ,O(1)

x in s # 所在判定,O(√n)
x not in s
for _ in s: # 走査,O(1)
for _ in reversed(s):

s.add(x) # 追加,O(√n),もうあればFalseを返す
s.discard(x) # 削除,O(√n),なければFalseを返す

# すべてO(√n),そんなものがなければNoneを返す
# Optional[int]みたいなよく分からない型で返ってくるのでint()推奨?
s.lt(x) # xより小さい最大
s.le(x) # x以下で最大
s.gt(x) # xより大きい最小 bisect_leftの要素の値
s.ge(x) # x以上で最小 bisect_rightの要素の値


s[i] # i番目、O(√n),√n番目以前だとさらに速い
s.pop(i)
s.index(x) # xより小さい要素の数,O(√n),bisect_leftと同じ
s.index_right(x) # x以下の要素の数,O(√n),bisect_rightと同じ

s == t # 同じ集合か,O(n)

s.count(x) # Multisetなら使える
```

```cardlink
url: https://github.com/tatyam-prime/SortedSet
title: "GitHub - tatyam-prime/SortedSet"
description: "Contribute to tatyam-prime/SortedSet development by creating an account on GitHub."
host: github.com
favicon: https://github.githubassets.com/favicons/favicon.svg
image: https://opengraph.githubassets.com/cb4ce75615ea41bd429793d7b9c326a4feaece26f4d3f74425a4578d531333b8/tatyam-prime/SortedSet
```

# Codon固有の注意

## Codonでの注意

入力の型推論や `map` 周りで詰まる場合は、リスト内包表記に寄せると安定します。

```python
a, b = [int(x) for x in input().split()]
arr = [int(x) for x in input().split()]
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

Codonではlistがhashableなのでdictのkeyにできたりsetに入れられたりなどする

Codonでは0/1とFalse/Trueは別の型
Codonのintは $[-2^{63}, 2^{63})$でlong longと同じ ($2^{63} = 9223372036854775807 \simeq 9.22 \times 10^{18}$)

参考: 
```cardlink
url: https://ikatakos.com/pot/programming_algorithm/python_tips/codon
title: "Codon（AtCoder Language Update 2025 に際して）    [いかたこのたこつぼ]"
host: ikatakos.com
```
