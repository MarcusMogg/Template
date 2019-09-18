## Tarjan求强连通分量

模板：受欢迎的牛（给出连边，求一个任意点可以到达的集合的大小）

```cpp
#include<stdio.h>
#define N 50010
#define min(a,b) (a>b?b:a)
struct Edge{
	int end,near;
}e[N<<1];
int head[N],cnt;
void add(int a,int b){
	e[++cnt].end=b;
	e[cnt].near=head[a];
	head[a]=cnt;
}
int sta[N],top,vis[N],n,m;
int col[N],num,val[N];
int dfn[N],low[N],tot;
void tarjan(int p){
	int i,q;
	low[p]=dfn[p]=++tot;
	sta[++top]=p;vis[p]=1;
	for(i=head[p];i;i=e[i].near){
		q=e[i].end;
		if(!dfn[q])tarjan(q),low[p]=min(low[p],low[q]);
		else if(vis[q])low[p]=min(low[q],low[p]);
	}
	if(dfn[p]==low[p]){
		num++;
		while(sta[top+1]!=p){
			q=sta[top];
			col[q]=num;
			vis[q]=0;
			val[num]++;
			top--;
		}
	}
}
int a[N],b[N],od[N];
int main(){
	int i;
	scanf("%d%d",&n,&m);
	for(i=0;i<m;++i){
		scanf("%d%d",&a[i],&b[i]);
		add(a[i],b[i]);
	}
	for(i=1;i<=n;i++)if(!dfn[i])tarjan(i);
	for(i=0;i<m;i++)if(col[a[i]]^col[b[i]])od[col[a[i]]]++;
	int ans=0;
	for(i=1;i<=num;i++){
		if(!od[i]){
			if(ans){
				printf("0");
				return 0;
			}
			ans=val[i];
		}
	}
	printf("%d",ans);
	return 0;
}
```

