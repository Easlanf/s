> 我们主要从程序入手，通过程序，可能理解起来更方便。


---
# dijkstra

主要看注释。
```
struct node {
	int num,d;//d指的是目前起点到这个点的距离
	bool operator<(const node &a) const {
		return d>a.d;
	}
};
int n,m,s;
int dis[10005],vis[10005];//dis[i]存起点到i的最短路
vector<pair<int,int> > adj[100005];//first存它到的点，second存权重
void build(int u,int v,int w)//建图，w是权重
{
	pair<int,int> tmp;
	tmp.first=v;
	tmp.second=w;
	adj[u].push_back(tmp);
}
void dijkstra(int s,int t)
{
	memset(vis,0,sizeof(vis));
	for(int i=0; i<=n; i++) {
		dis[i]=2147483647;
	}
	dis[s]=0;
	priority_queue<node> q;
	node tmp;
	tmp.num=s;
	tmp.d=0;//起点到起点肯定为0
	q.push(tmp);
	while (!q.empty()) {
		int u=q.top().num;
		if(u==t) {//找到终点，返回
			return ;
		}
		q.pop();
		if(vis[u]) {
			continue;
		}
		vis[u]=1;
		for (int i=0; i<adj[u].size(); i++) {
			int v=adj[u][i].first;
			int w=adj[u][i].second;
			if (dis[v]>dis[u]+w) {//松弛操作，即dis[v]（起点到点v的距离）比dis[u]（起点到点u的距离）+u到v的权重大，那么就将dis[v]替换为dis[u]+u到v的权重）
				dis[v]=dis[u]+w;
				tmp.num=v;
				tmp.d=dis[v];
				q.push(tmp);
			}
		}
	}
}
int main(void)
{
	//freopen(".in","r",stdin);
	//freopen(".out","w",stdout);
	scanf("%d %d %d",&n,&m,&s);
	for(int i=1; i<=m; i++) {
		int u,v,w;
		scanf("%d %d %d",&u,&v,&w);
		build(u,v,w);
	}
	int sum=0;
	for(int i=1; i<=n; i++) {
		if(i!=s) {
			dijkstra(s,i);
			printf("%d ",dis[i]);
		} else if(i==s) {//简单优化一次
			 printf("0 ");
		}
	}
	return 0;
}
```
当然，除此之外，dijkstra还可以一次性算出从起点到每个点的最短路，会跟快。

```
void dijkstra(int s)
{
	memset(vis,0,sizeof(vis));
	for(int i=0; i<=n; i++) {
		dis[i]=2147483647;
	}
	dis[s]=0;
	priority_queue<node> q;
	node tmp;
	tmp.num=s;
	tmp.d=0;
	q.push(tmp);
	while (!q.empty()) {
		int u=q.top().num;
	//	if(u==t) {
	//		return ;
	//	}
    //将这个if删掉，不判断终点，这样就可以遍历到所有点
		q.pop();
		if(vis[u]) {
			continue;
		}
		vis[u]=1;
		for (int i=0; i<adj[u].size(); i++) {
			int v=adj[u][i].first;
			int w=adj[u][i].second;
			if (dis[v]>dis[u]+w) {//松弛操作
				dis[v]=dis[u]+w;
				tmp.num=v;
				tmp.d=dis[v];
				q.push(tmp);
			}
		}
	}
}
```
dijkstra主要是松弛操作，其他的很好理解。

---

# SPFA

> 关于SPFA，它已经死了

虽然它死了，但还是有必要说一下，因为它是一个可以处理负权的算法。

> 维护一个队列，里面存放所有需要进行更新的点。 初始时队列中只有一个源点S（d[s]=0）。 每次取出队头的点u， 尝试松弛u 的所有出边<u,v> ， 若能够松弛 （d[u]+w[u][v]<d[v])，则改进d[v]。此 时由于s->v的最短距离d[v]变小了，有可能通过v可以改进其它结点，将其push进队。 这样一直迭代下去直到队列为空，也就是 d[i]都确定下来，结束算法。 若一个点最短路被改进的次数达到V（结点数），则有负权环。 那么可以用spfa算法判断图有无负权环。 

SPFA算法复杂度的理论上界为O(VE)。在实际情况下（即出题人不故意卡的话）往往能够达到O(10E)。


```
int vis[10005];
int dis[10005];
void spfa()
{
	for (int i=1; i<=n; ++i) {
		dis[i]=0x7fffffff;//就是2147483647
	}
	queue<int> q;//注意，这里是普通队列，并且没有结构体，存的就是入队的点的编号
	q.push(s);
	vis[s]=1;
	dis[s]=0;//还是，起点到起点为0
	while (!q.empty()) {
		int u=q.front();
		q.pop();
		vis[u]=0;
		for (int i=0; i<adj[u].size(); ++i) {
			int v=adj[u][i].v;
			int w=adj[u][i].w;//忘写上了，adj是一个vector，存v和w，同dij算法中的含义
			if(dis[u]+w<dis[v]) {//松弛操作
				dis[v]=dis[u]+w;
				if(!vis[v]) {
					vis[v]=1;
					q.push(v);
				}
			}
		}
	}
}
```
当然，除非数据范围内有负权，否则不建议用SPFA，容易被卡。如果出题人数据范围内有负权还卡SPFA，emm，那就算出题人nb了。

---

提醒一句，如果没有边权或点权，个人觉得Bfs和Dfs才是王道（因为比起这两个，Bfs和Dfs好写）

那么关于Dij和SPFA算法的介绍就到这里了，谢谢大家的光顾！
