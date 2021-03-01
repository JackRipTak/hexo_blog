---
title: Dinic 算法复习
date: 2021-03-01 19:30:02
tags: 算法
---
dinic算法的核心在于多路同时增广 

考虑简化情况: 

如果网络在一个DAG上,那么只需要dfs一边就能搞定

然后对于回退边只需要再进行一次Bfs-Dfs就行了

对于每一次的bfs DAG只需要把所有的最短路都加进去就行了

再加上当前弧优化就是记录每次dfs点的当前弧,这样能够保证不会被重复遍历

```
#include<bits/stdc++.h>

using namespace std;

namespace Tzh{
	
	typedef long long ll;
	const int maxn=210,maxm=5010;
	int n,m,s,t,head[maxn],dep[maxn],cur[maxn];
	int cnt=1;

	struct ed{
		int next,to;
		ll w;
	}e[maxm<<2];

	void add(int u,int v,ll w){
		e[++cnt]=(ed){head[u],v,w}; head[u]=cnt; 
	}

	bool bfs(){
		memset(dep,0,sizeof(dep)); dep[s]=1;
		queue<int > q; q.push(s);
		while(!q.empty()){
			int now=q.front(); q.pop();
			for(int i=head[now];i;i=e[i].next){
				int tt=e[i].to; 
				if(!dep[tt]&&e[i].w>0){
					q.push(tt); dep[tt]=dep[now]+1;
				}
			}
		}
		return dep[t];
	}

	ll dfs(int now,ll flow){
		if(now==t||!flow) return flow;
		ll used=0;
		for(int &i=cur[now];i;i=e[i].next){
			int tt=e[i].to; 
			if(dep[tt]!=dep[now]+1||e[i].w<=0) continue;
			ll w=dfs(tt,min(e[i].w,flow-used));
			e[i].w-=w; e[i^1].w+=w; used+=w;
			if(flow==used) return used;
		}
		return used;
	}

	ll dinic(){ 
		ll flow=0;
		while(bfs()){ 
			memcpy(cur,head,sizeof(cur));
			flow+=dfs(s,INT_MAX);
		}
		return flow;
	}

	void work(){
		scanf("%d%d%d%d",&n,&m,&s,&t);
		for(int i=1;i<=m;i++) {
			int u,v; ll w;
			scanf("%d%d%lld",&u,&v,&w);
			add(u,v,w); add(v,u,0);
		}
		printf("%lld",dinic());
		return ;
	}
}

int main(){
	Tzh::work();
	return 0;
}
```