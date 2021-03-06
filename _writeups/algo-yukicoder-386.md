---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/386/
  - /blog/2016/07/02/yuki-386/
date: 2016-07-02T00:23:27+09:00
edited: 2020-03-02T00:00:00+09:00
tags: [ "competitive", "writeup", "yukicoder", "lowest-common-ancestor", "heavy-light-decomposition", "segment-tree", "graph", "tree" ]
"target_url": [ "https://yukicoder.me/problems/no/386" ]
---

# Yukicoder No.386 貪欲な領主

星3だしタイトル的にも貪欲解がありそうだなと思ったが、重軽分解を貼れば済むので貼ってしまった。
最小共通祖先は必須であるが、重軽分解とsegment木に関しては不要だったようだ。
ライブラリの使い方のドキュメントとしては有用であるのでそのまま置いておく。

重軽分解とsegment木の典型的な使用で、更新操作がなく、segment木の管理する要素がmonoidだけでなく群でもあるなら、重軽分解とsegment木なしで書き直せるようだ。
木の根から各点への重みをそれぞれ持っておけばよい。

関連: [Vertex Add Path Sum - Library Checker](https://judge.yosupo.jp/problem/vertex_add_path_sum)

## solution

根から各点までの重みを求めておき、最小共通祖先を求めれば答えは計算できる。sparse table 等で $O(1)$ LCA をすれば $O(N \log N + M)$。

頂点の重み$v$を$w_v$、根から頂点$v$までの重みの総和を$d_v$とし、頂点$u,v$の最小共通祖先$\mathrm{lca}(u,v)$とする。
クエリ$a,b,c$に関しその答え$\mathrm{ans} = (d_a - d\_{\mathrm{lca}(a,b)}) + (d_b - d\_{\mathrm{lca}(a,b)}) + w\_{\mathrm{lca}(a,b)}$である。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <map>
#include <functional>
#include <cmath>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
typedef long long ll;
using namespace std;

struct heavy_light_decomposition_t {
    int n; // |V'|
    vector<int> a; // V ->> V' epic
    vector<vector<int> > path; // V' -> V*, bottom to top order, disjoint union of codomain matchs V
    vector<map<int,int> > pfind; // V' * V -> int, find in path
    vector<int> parent; // V' -> V
    heavy_light_decomposition_t(int v, vector<vector<int> > const & g) {
        n = 0;
        a.resize(g.size());
        dfs(v, -1, g);
    }
    int dfs(int v, int p, vector<vector<int> > const & g) {
        int heavy_node = -1;
        int heavy_size = 0;
        int desc_size = 1;
        for (int w : g[v]) if (w != p) {
            int size = dfs(w, v, g);
            desc_size += size;
            if (heavy_size < size) {
                heavy_node = w;
                heavy_size = size;
            }
        }
        if (heavy_node == -1) {
            a[v] = n;
            n += 1;
            path.emplace_back();
            path.back().push_back(v);
            pfind.emplace_back();
            pfind.back()[v] = 0;
            parent.push_back(p);
        } else {
            int i = a[heavy_node];
            a[v] = i;
            pfind[i][v] = path[i].size();
            path[i].push_back(v);
            parent[i] = p;
        }
        return desc_size;
    }
};

struct lowest_common_ancestor_t {
    vector<vector<int> > a;
    vector<int> depth;
    lowest_common_ancestor_t(int v, vector<vector<int> > const & g) {
        int n = g.size();
        int l = 1 + floor(log2(n));
        a.resize(l);
        repeat (k,l) a[k].resize(n, -1);
        depth.resize(n);
        dfs(v, -1, 0, g, a[0], depth);
        repeat (k,l-1) {
            repeat (i,n) {
                if (a[k][i] != -1) {
                    a[k+1][i] = a[k][a[k][i]];
                }
            }
        }
    }
    static void dfs(int v, int p, int current_depth, vector<vector<int> > const & g, vector<int> & parent, vector<int> & depth) {
        parent[v] = p;
        depth[v] = current_depth;
        for (int w : g[v]) if (w != p) {
            dfs(w, v, current_depth + 1, g, parent, depth);
        }
    }
    // find lca of x, y
    int operator () (int x, int y) const { // O(log N)
        int l = a.size();
        if (depth[x] < depth[y]) swap(x,y);
        repeat_reverse (k,l) {
            if (a[k][x] != -1 and depth[a[k][x]] >= depth[y]) {
                x = a[k][x];
            }
        }
        assert (depth[x] == depth[y]);
        assert (x != -1);
        if (x == y) return x;
        repeat_reverse (k,l) {
            if (a[k][x] != a[k][y]) {
                x = a[k][x];
                y = a[k][y];
            }
        }
        assert (x != y);
        assert (a[0][x] == a[0][y]);
        return a[0][x];
    }
    // find the descendant of x for y
    int descendant (int x, int y) const {
        assert (depth[x] < depth[y]);
        int l = a.size();
        repeat_reverse (k,l) {
            if (a[k][y] != -1 and depth[a[k][y]] >= depth[x]+1) {
                y = a[k][y];
            }
        }
        assert (a[0][y] == x);
        return y;
    }
};

template <typename T>
struct segment_tree { // on monoid
    int n;
    vector<T> a;
    function<T (T,T)> append; // associative
    T unit;
    template <typename F>
    segment_tree(int a_n, T a_unit, F a_append) {
        n = pow(2,ceil(log2(a_n)));
        a.resize(2*n-1, a_unit);
        unit = a_unit;
        append = a_append;
    }
    void point_update(int i, T z) {
        a[i+n-1] = z;
        for (i = (i+n)/2; i > 0; i /= 2) {
            a[i-1] = append(a[2*i-1], a[2*i]);
        }
    }
    T range_concat(int l, int r) {
        return range_concat(0, 0, n, l, r);
    }
    T range_concat(int i, int il, int ir, int l, int r) {
        if (l <= il and ir <= r) {
            return a[i];
        } else if (ir <= l or r <= il) {
            return unit;
        } else {
            return append(
                    range_concat(2*i+1, il, (il+ir)/2, l, r),
                    range_concat(2*i+2, (il+ir)/2, ir, l, r));
        }
    }
};

template <typename T>
T path_concat(heavy_light_decomposition_t & hl, vector<segment_tree<T> > & sts, int v, int w) {
    auto append = sts.front().append;
    auto unit   = sts.front().unit;
    T acc = unit;
    int i = hl.a[v];
    if (hl.a[w] == i) {
        assert (hl.pfind[i][v] <= hl.pfind[i][w]); // v must be a descendant of w
        acc = append(acc, sts[i].range_concat(hl.pfind[i][v], hl.pfind[i][w]+1));
    } else {
        acc = append(acc, sts[i].range_concat(hl.pfind[i][v], hl.path[i].size()));
        acc = append(acc, path_concat(hl, sts, hl.parent[i], w));
    }
    return acc;
}

template <typename T>
T path_concat(heavy_light_decomposition_t & hl, lowest_common_ancestor_t & lca, vector<segment_tree<T> > & sts, int x, int y) {
    auto append = sts.front().append;
    auto unit   = sts.front().unit;
    int z = lca(x, y);
    T acc = unit;
    if (x != z) acc = append(acc, path_concat(hl, sts, x, lca.descendant(z, x)));
    if (y != z) acc = append(acc, path_concat(hl, sts, y, lca.descendant(z, y)));
    acc = append(acc, path_concat(hl, sts, z, z));
    return acc;
}

int main() {
    // input
    int n; scanf("%d", &n);
    vector<vector<int> > g(n);
    repeat (i,n-1) {
        int a, b; scanf("%d%d", &a, &b);
        g[a].push_back(b);
        g[b].push_back(a);
    }
    vector<int> u(n); repeat (i,n) scanf("%d", &u[i]);
    // prepare
    const int root = 0;
    heavy_light_decomposition_t hl(root, g);
    vector<segment_tree<ll> > sts;
    repeat (i,hl.n) {
        sts.emplace_back(hl.path[i].size(), 0, [](ll a, ll b) { return a + b; });
    }
    repeat (i,n) {
        int l = hl.a[i];
        sts[l].point_update(hl.pfind[l][i], u[i]);
    }
    lowest_common_ancestor_t lca(root, g);
    // run
    ll ans = 0;
    int m; scanf("%d", &m);
    repeat (i,m) {
        int a, b, c; scanf("%d%d%d", &a, &b, &c);
        ans += path_concat(hl, lca, sts, a, b) * c;
    }
    // output
    printf("%lld\n", ans);
    return 0;
}
```
