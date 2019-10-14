## Dijkstra

```cpp
#include<cstdio>
#include<queue>
using namespace std;
int n,m,s;
struct Edge{
	int end,near;
	long long len;
}edge[500010];
long long len[500010];
int head[500010],cnt=1;
void add(int begin,int end,long long len){
	edge[cnt].end=end;
	edge[cnt].len=len;
	edge[cnt].near=head[begin];
	head[begin]=cnt++;
}
struct num{
	int len,x;
	bool operator < (const num &a)const{
		return len>a.len;
	}
};
int vis[500010];
priority_queue<num> q;
void dijkstra(){
	q.push((num){0,s});
	while(!q.empty()){
		int p=q.top().x;
		q.pop();
		if(vis[p])continue;
		vis[p]=1;
		for(int i=head[p];i;i=edge[i].near){
			int e=edge[i].end;
			if(len[e]>len[p]+edge[i].len){
				len[e]=len[p]+edge[i].len;
				q.push((num){len[e],e});
			}
		}
	}
}
int main(){
	int i,u,v;
	long long w;
	scanf("%d%d%d",&n,&m,&s);
	for(i=0;i<m;i++){
		scanf("%d%d%lld",&u,&v,&w);
		add(u,v,w);
	}
	for(i=1;i<=n;i++)len[i]=214748364700LL;len[s]=0;
	dijkstra();
	for(i=1;i<=n;i++){
		if(len[i]<2147483647)printf("%lld ",len[i]);
		else printf("2147483647 ");
	}
	return 0;
}
```

