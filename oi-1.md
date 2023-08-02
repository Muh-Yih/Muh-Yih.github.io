# [ABC187E] Through Path 题解

当前位置：[首页](index.md) > [信竞相关](oi.md) > [[ABC187E] Through Path 题解](oi-1.md)

发布时间：2023年8月2日 11:06

## 题意

给定一棵 N 节点的树，边形如 (u_i, v_i)。维护以下操作 Q 次：

- op_i = 1，指定一条边 i，将所有从 u_i 出发，**不经过这条边就能到达**的点，点权加 k。
- op_i = 2，指定一条边 i，将所有从 v_i 出发，**不经过这条边就能到达**的点，点权加 k。

输出最终每个点的点权。初始点权为 0。

## 分析

刚开始看到这道题的时候，我想的是树链剖分——然而很快就~~因码力不足~~将其否决，于是开始考虑树上差分。

让我们来看看样例：

```
7
2 1
2 3
4 2
4 5
6 1
3 7
7
2 2 1
1 3 2
2 2 4
1 6 8
1 3 16
2 4 32
2 1 64
```

规定 1 号节点为根节点，把这棵树画出来，大概是这样：

![](https://cdn.luogu.com.cn/upload/image_hosting/8hxywvyh.png)

考虑第 i 次操作为 op_i,e_i,k_i，无论 op_i=1 还是 op_i=2，都会选中一条边，其中一个顶点作为出发点。

记出发点为 st，另一个顶点为 at，节点 x 深度为 depth_x（depth_1=1）。

若 depth_{st} < depth_{at}，则修改的节点为树上除 at 的子树（包括 at）外的所有节点；若 depth_{st} > depth_{at}，则修改的节点为 st 的子树（包括 st）。

比如，对于操作 ```2 2 1``` ，有 st=v_2=3,at=u_2=2;depth_{st}=depth_3=3,depth_{at}=depth_2=2。此时 depth_{st} > depth_{at}。修改节点如下：

![](https://cdn.luogu.com.cn/upload/image_hosting/xhi5n480.png)

对于操作 ```1 6 8``` ，有 st=u_6=3,at=v_6=7;depth_{st}=depth_3=3,depth_{at}=depth_7=4。此时 depth_{st} < depth_{at}。修改节点如下：

![](https://cdn.luogu.com.cn/upload/image_hosting/5h36577g.png)

于是我们可以建一个差分数组 d[N] ：对于操作 op_i,e_i,k_i，若 depth_{st} < depth_{at}，则 d[1]+=k_i,d[at]-=k_i；若 depth_{st} > depth_{at}，则 d[st]+=k_i。

最后从根往下统计答案即可。

总体思路：

- 处理深度
- 每次询问修改差分数组
- 统计答案

我的代码中使用 DFS 处理深度，时间复杂度 O(N)；每次询问修改差分数组，时间复杂度 O(Q)；使用 DFS 统计答案，时间复杂度 O(N)；总时间复杂度 O(N+Q)。

## 代码
```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N=410000;
int u[N],v[N],depth[N],chafen[N],ans[N];
int n,q;
vector <int> mp[N];
void depdfs (int x,int fa,int dep)
{
	depth[x]=dep;
	for (int i=0;i<mp[x].size();i++)
	{
		int pt=mp[x][i];
		if (pt!=fa) depdfs (pt,x,dep+1);
	}
}
void chafendfs (int x,int fa,int v)
{
	v+=chafen[x];
	ans[x]=v;
	for (int i=0;i<mp[x].size();i++)
	{
		int pt=mp[x][i];
		if (pt!=fa) chafendfs (pt,x,v);
	}
}
signed main()
{
	scanf ("%lld",&n);
	for (int i=1;i<n;i++)
	{
		scanf ("%lld%lld",u+i,v+i);
		mp[u[i]].push_back(v[i]);mp[v[i]].push_back(u[i]);
	}
	depdfs (1,0,1);
    scanf ("%lld",&q);
	for (int i=1;i<=q;i++)
	{
		int op,e,val;
		scanf ("%lld%lld%lld",&op,&e,&val);
		int ch,nch,chd,nchd;
		if (op==1)
		{
			ch=u[e],nch=v[e];
			chd=depth[ch],nchd=depth[nch];
			if (chd<nchd) chafen[1]+=val,chafen[nch]-=val;
			else chafen[ch]+=val;
		}
		if (op==2)
		{
			ch=v[e],nch=u[e];
			chd=depth[ch],nchd=depth[nch];
			if (chd<nchd) chafen[1]+=val,chafen[nch]-=val;
			else chafen[ch]+=val;
		}
	}
	chafendfs (1,0,0);
	for (int i=1;i<=n;i++) printf ("%lld\n",ans[i]);
	return 0;
}
```

---
###### 外部链接
###### [我的洛谷博客](https://muhyih.blog.luogu.org/)
###### [我的 LOFTER 博客](https://seven-celsius-sunny.lofter.com/)
