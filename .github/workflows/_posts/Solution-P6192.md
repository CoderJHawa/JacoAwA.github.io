---
layout: post
title: "Solution-P6192"
date: 2024-09-15
---

[题目传送门](luogu.com.cn/problem/P6192)

前置知识：最小斯坦纳树

最小斯坦纳树可以理解为升级版的最小生成树。

首先给定图 $G=(V,E)$,以及 $V$ 的子集 $U$，$U$ 中的点为终端节点。最小斯坦纳树即求包含所有终端节点的 $G$ 的子树中用到边权总和最小的。

- 在最小斯坦纳树中，若所有顶点都为终端节点，这个问题就是最小生成树问题。

- 最小斯坦纳树已经被证明是 NP-Hard 问题，所以可能不存在高效算法


------

举个例子

![最小斯坦纳树-图1](https://cdn.luogu.com.cn/upload/image_hosting/6q77icyc.png)


如上图，已知其终端节点为 $A,B,C,F$，则其最小斯坦纳树如下：

![最小斯坦纳树-图2](https://cdn.luogu.com.cn/upload/image_hosting/42p9lm06.png)

------

思路：考虑状压DP

可以将 $dp_{i,s}$ 看作

考虑以下两种转移方式:

- $dp_{i,S}=dp_{i,T}+dp_{i,S-T} \quad \quad (T\subseteq S )$

- $dp_{i,S}=dp_{i,s}+w(i,j)$

显然只需枚举 $S$ 的子集 $T$ 即可,然后发现可以用最短路跑一遍，于是故解法为状压DP+最短路


代码如下：

```cpp
#include<bits/stdc++.h>
#define rep(a,b,c) for(int a=b;a<=c;a++)
#define LL long long
using namespace std;
inline int read();
void write(int x);
struct Edge{int to,next,dis;}edge[1010];
int n=read(),m=read(),k=read();
int head[1010],tree[1010],cnt;
int dp[1010][5010],vis[1010],key[1010];
priority_queue<pair<int,int>,vector<pair<int,int>>,greater<pair<int,int>>>q;
void add(int u,int v,int w){
	edge[++cnt].next=head[u],edge[cnt].to=v,edge[cnt].dis=w;
	head[u]=cnt,tree[cnt]=v; 
}
#define P pair<int,int>
void dijkstra(int s){
	memset(vis,0,sizeof(vis));
	while(!q.empty()){
		pair<int,int> p=q.top();
		q.pop();	
		if(vis[p.second]) continue;
		vis[p.second]=1;
		for(int i=head[p.second];i;i=edge[i].next){
			if(dp[tree[i]][s]>dp[p.second][s]+edge[i].dis){
				dp[tree[i]][s]=dp[p.second][s]+edge[i].dis;
				q.push(P(dp[tree[i]][s],tree[i]));
			}
		}
	}
}
void init(){
	int u,v,w;
	memset(dp,0x3f,sizeof(dp));
	for(int i=1;i<=m;i++){
		u=read(),v=read(),w=read();
		add(u,v,w);
		add(v,u,w);
	}
	for(int i=1;i<=k;i++){
		key[i]=read();
		dp[key[i]][1<<(i-1)]=0;
	}
}
int main(){
	//freopen("MST.in","r",stdin);
	//freopen("MST.out","w",stdout);
	init();
	for(int s=1;s<(1<<k);s++){
		for(int i=1;i<=n;i++){
			for(int j=s&(s-1);j;j=s&(j-1))dp[i][s]=min(dp[i][s],dp[i][j]+dp[i][s^j]);
			if(dp[i][s]!=0x3f) q.push(P(dp[i][s],i));	
		}
		dijkstra(s);
	}
	write(dp[key[1]][(1<<k)-1]); 
	return 0;
}
inline int read(){
    int x=0,f=1;
    char ch=getchar();
    while(ch<'0'||ch>'9'){
        if(ch=='-')f=-1;
        ch=getchar();
    }
    while(ch>='0'&&ch<='9'){
    	x=x*10+ch-'0';
		ch=getchar();
	}
    return x*f;
}
void write(int x){
    if(x<0)putchar('-'),x=-x;
    if(x>9)write(x/10);
    putchar(x%10+'0');
    return;
}
```
