## 最近公共祖先

```cpp
#include<stdio.h>
#define N 500010
int n,m,s;
struct Edge{
	int e,n;
}e[N<<1];
int cnt;
int d[N],fa[22][N],lg[N],hd[N];
void add(int begin,int e){
	e[++cnt].e=e;
	e[cnt].n=hd[begin];
	hd[begin]=cnt;
}
void dfs(int now,int f){
	d[now]=d[f]+1;
	fa[0][now]=f;
	int i;
	for(i=1;(1<<i)<=d[now];i++)
		fa[i][now]=fa[i-1][fa[i-1][now]];
	for(i=hd[now];i;i=e[i].n){
		int p=e[i].e;
		if(p!=f)dfs(p,now);
	}
}
int lca(int x,int y){
	int i;
	if(d[x]<d[y]){int t=x;x=y;y=t;}
	while(d[x]>d[y])
		x=fa[lg[d[x]-d[y]]][x];
	if(x==y)return x;
	for(i=lg[d[x]];~i;i--)
		if(fa[i][x]!=fa[i][y])
			x=fa[i][x],y=fa[i][y];
	return fa[0][x];
}
int main(){
	int i,x,y;
	scanf("%d%d%d",&n,&m,&s);
	for(i=0;i<n-1;i++){
		scanf("%d%d",&x,&y);
		add(x,y);add(y,x);
	}
	dfs(s,0);
	for(i=2;i<=n;i++)lg[i]=lg[i-1]+(1<<(lg[i-1]+1)==i);
	for(i=0;i<m;i++){
		scanf("%d%d",&x,&y);
		printf("%d\n",lca(x,y));
	}
	return 0;
}
```

