## 2-sat问题

给定一组逻辑判断规则，求一组布尔量的取值。

做法：$(u,v)$连边代表若$u$则$v$。每个布尔量分为两个点，分别代表这个量取值为$0$或$1$。

用$tarjan$做出强连通分量并染色后，若拆点在同一分量内则无解，否则颜色是按照逆拓扑序染的（$tarjan$特性），按照尽量满足拓扑序靠后的方案染色（靠前则更可能影响到后面变量取值），故满足颜色号小的。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define N 2000004
struct Edge{
	int e,n;
}e[N<<1];
int hd[N],Cnt;
void add(int a,int b){e[++Cnt].e=b;e[Cnt].n=hd[a];hd[a]=Cnt;}
int col[N],sta[N],dfn[N],low[N],cnt,tot,top,vis[N];
void tarjan(int p){
	int i,q;
	dfn[p]=low[p]=++cnt;
	sta[++top]=p;vis[p]=1;
	for(i=hd[p];i;i=e[i].n){
		q=e[i].e;
		if(!dfn[q])tarjan(q),low[p]=min(low[p],low[q]);
		else if(vis[q])low[p]=min(low[p],dfn[q]);
	}
	if(low[p]==dfn[p]){
		tot++;
		do{
			q=sta[top--];
			col[q]=tot;
			vis[q]=0;
		}while(q!=p);
	}
}
int main(){
	int i,n,m,x,y,a,b;
	scanf("%d%d",&n,&m);
	for(i=0;i<m;i++){
		scanf("%d%d%d%d",&x,&a,&y,&b);
		add(x+n*(a&1),y+n*(b^1)),add(y+n*(b&1),x+n*(a^1));
	}
	for(i=1;i<=n<<1;i++)if(!dfn[i])tarjan(i);
	for(i=1;i<=n;i++)if(col[i]==col[i+n])return printf("IMPOSSIBLE"),0;
	printf("POSSIBLE\n");
	for(i=1;i<=n;i++)printf("%d ",col[i]<col[i+n]);
	return 0;
}
```

