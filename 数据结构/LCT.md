## Link-Cut Tree

```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
#include<map>
#include<cstring>
#include<cmath>
#include<cstdlib>
#include<set>
#include<unordered_map>
#include<vector>
typedef long long ll;
using namespace std;
#define N 300010
#define ls(p) tr[p][0]
#define rs(p) tr[p][1]
int f[N],tr[N][2],lazy[N];
int v[N],sum[N];//需要改动地方1
int st[N];
int nroot(int p){//不是一个splay的根
	return ls(f[p])==p||rs(f[p])==p;
	//如果是根，其父亲不认儿子，返回0
}
void pushup(int p){//上传，需要改动地方2
	sum[p]=sum[ls(p)]^sum[rs(p)]^v[p];
}
void pushr(int p){//翻转
	int t=ls(p);ls(p)=rs(p);rs(p)=t;
	lazy[p]^=1;
}
void pushdown(int p){//下放懒标记
	if(lazy[p]){
		if(ls(p))pushr(ls(p));
		if(rs(p))pushr(rs(p));
		lazy[p]=0;
	}
}
void rotate(int p){//一次旋转
	int y=f[p],z=f[y],k=(rs(y)==p),w=tr[p][!k];
	if(nroot(y))tr[z][tr[z][1]==y]=p;
	tr[p][!k]=y;tr[y][k]=w;
	if(w)f[w]=y;
	f[y]=p;f[p]=z;
	pushup(y);
}
void splay(int p){//splay p to root
	int y=p,top=0,z;
	st[++top]=p;//存当前点到根的路径，下放懒标记
	while(nroot(y))st[++top]=y=f[y];
	while(top)pushdown(st[top--]);
	while(nroot(p)){
		y=f[p];z=f[y];
		if(nroot(y))rotate((ls(y)==p)^(ls(z)==y)?p:y);
		rotate(p);
	}
	pushup(p);
}
//LCT
void access(int x){//将p与其原树splay森林对应的根连起来
	int y=0;
	while(x)splay(x),rs(x)=y,pushup(x),y=x,x=f[x];
}
void makeroot(int x){//将p变成根
	access(x);splay(x);pushr(x);
}
int findroot(int x){//x所在原树树根
	access(x);splay(x);
	while(ls(x))pushdown(x),x=ls(x);
	splay(x);
	return x;
}
void split(int x,int y){//提取出单独的一条x-y路径（扔到一个splay,y为根）
	makeroot(x);
	access(y);splay(y);
}
void link(int x,int y){//link x-y
	makeroot(x);
	if(findroot(y)!=x)f[x]=y;
}
void cut(int x,int y){//删掉xy边
	makeroot(x);
	if(findroot(y)==x&&f[y]==x&&!ls(y))//xy直接相连
		f[y]=ls(x)=0,pushup(x);
}
int main(){
	int i,n,m;
	scanf("%d%d",&n,&m);
	for(i=1;i<=n;i++)scanf("%d",&v[i]);
	while(m--){
		int opt,x,y;
		scanf("%d%d%d",&opt,&x,&y);
		if(opt==0)split(x,y),printf("%d\n",sum[y]);
		else if(opt==1)link(x,y);
		else if(opt==2)cut(x,y);
		else splay(x),v[x]=y;//传上再改，否则影响正确性
	}
	return 0;
}
```

