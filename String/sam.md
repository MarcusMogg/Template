## 后缀自动机

洛谷模板：出现次数$$\times $$长度最大值

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define N 2000010
#define P 26
char a[N];
struct Edge{int e,n;}e[N<<1];
int hd[N],cnt;
void add(int a,int b){e[++cnt].e=b;e[cnt].n=hd[a];hd[a]=cnt;}
struct SAM{
	int tr[N<<1][P],fa[N<<1],len[N<<1],siz[N<<1];
	int cnt,last;
	void init(){cnt=last=1;}//dont forget!!!
	void add(int c){
		int p=last,np=++cnt;
		siz[np]=1;
		last=np;
		len[np]=len[p]+1;
		while(p&&!tr[p][c])tr[p][c]=np,p=fa[p];
		if(!p)fa[np]=1;
		else{
			int q=tr[p][c];
			if(len[q]==len[p]+1)fa[np]=q;
			else{
				int nq=++cnt;len[nq]=len[p]+1;
				memcpy(tr[nq],tr[q],sizeof(tr[q]));
				fa[nq]=fa[q];
				fa[q]=fa[np]=nq;
				while(tr[p][c]==q)tr[p][c]=nq,p=fa[p];
			}
		}
	}
	void insert(char a[]){
		int i;
		for(i=0;a[i];i++)add(a[i]-'a');//can be changed
	}
}sam;
ll ans;
void dfs(int x){
	int i;
	for(i=hd[x];i;i=e[i].n){
		int q=e[i].e;
		dfs(q);
		sam.siz[x]+=sam.siz[q];
	}
	if(sam.siz[x]!=1)ans=max(ans,sam.siz[x]*1LL*sam.len[x]);
}
int main(){
	int i;
	scanf("%s",a);
	sam.init();
	sam.insert(a);
	for(i=2;i<=sam.cnt;i++)add(sam.fa[i],i);
	dfs(1);
	printf("%lld",ans);
	return 0;
}
```

$$ExSAM$$：$$[ZJOI2014]$$诸神眷顾的幻想乡

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define N 4000010
#define P 11
struct Edge{int e,n;}e[N<<1];
int hd[N],cnt,deg[N];
void add(int a,int b){e[++cnt].e=b;e[cnt].n=hd[a];hd[a]=cnt;}
struct ExtendedSAM{
	int len[N],fa[N],tr[N][P];
	int siz;
	void init(){
		int i;
		siz=1;len[0]=-1;
		for(i=0;i<P;i++)tr[0][i]=1;
	}
	int insert(int s,int p){
		int np=++siz;
		len[np]=len[p]+1;
		while(!tr[p][s])tr[p][s]=np,p=fa[p];
		int q=tr[p][s];
		if(len[p]+1==len[q])fa[np]=q;
		else{
			int nq=++siz;
			len[nq]=len[p]+1;
			fa[nq]=fa[q];
			fa[q]=fa[np]=nq;
			memcpy(tr[nq],tr[q],sizeof(tr[q]));
			while(tr[p][s]==q)tr[p][s]=nq,p=fa[p];
		}
		return np;
	}
}sam;
int a[N],n,c;
void dfs(int now,int f,int pos){
	int i,nxt=sam.insert(a[now],pos);
	for(i=hd[now];i;i=e[i].n){
		int q=e[i].e;
		if(q!=f)dfs(q,now,nxt);
	}
}
int main(){
	int i,u,v;
	sam.init();
	scanf("%d%d",&n,&c);
	for(i=1;i<=n;i++)scanf("%d",&a[i]);
	for(i=1;i<n;i++)
		scanf("%d%d",&u,&v),
		add(u,v),add(v,u),
		deg[u]++,deg[v]++;
	for(i=1;i<=n;i++)if(deg[i]==1)dfs(i,0,1);
	ll ans=0;
	for(i=2;i<=sam.siz;i++)ans+=sam.len[i]-sam.len[sam.fa[i]];
	printf("%lld",ans);
	return 0;
}
```

