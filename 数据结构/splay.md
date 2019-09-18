## Splay

```cpp
#include<bits/stdc++.h>
using namespace std;
#define root t[0].s[1]
struct Tree{
	int s[2];//son
	int sum;//总 数字 数 
	int cnt;//本节点数字的出现次数
	int f;//father
	int data;
}t[200010];
int size,tot;//最大节点序号，总 数字 数 
void upd(int x){
	t[x].sum=t[t[x].s[0]].sum+t[t[x].s[1]].sum+t[x].cnt;//更新节点 
}
int id(int x){//查询该节点为左儿子还是右儿子 
	return x==t[t[x].f].s[0]?0:1;
}
void connect(int x,int fa,int son){//x->fa,fa.son->x 
	t[x].f=fa;
	t[fa].s[son]=x;
}
void rotate(int x){//x为中心的旋转 
	int y=t[x].f,idx=id(x),idy=id(y),B=t[x].s[idx^1],R=t[y].f;
	connect(B,y,idx);
	connect(y,x,idx^1);
	connect(x,R,idy);
	upd(x);upd(y);
}
void splay(int now,int to){//把now转为to的父亲的孩子节点（转到to的位置） 
	to=t[to].f;
	while(to!=t[now].f){
		int up=t[now].f;
		if(to==t[up].f)rotate(now);//只能翻上去一次 
		else if(id(now)==id(up)){
			rotate(up);
			rotate(now);
		}
		else{
			rotate(now);
			rotate(now);
		}
	}
}
int create(int v,int fa){//建立节点 
	t[++size].data=v;
	t[size].f=fa;
	t[size].cnt=t[size].sum=1;
	return size;
}
void push(int v){//找到插入的位置插入 
	int sign=0,nxt;
	tot++;
	if(!root)root=create(v,0);
	else{
		int now=root;
		while(now){
			t[now].sum++;
			if(t[now].data==v){
				t[now].cnt++,sign=now;
				break;
			}
			nxt=t[now].data<v?1:0;
			if(!t[now].s[nxt]){
				sign=t[now].s[nxt]=create(v,now);
				break;
			}
			now=t[now].s[nxt];
		}
	}
	splay(sign,root);
}
int find(int v){
	int now=root,nxt;
	while(now){
		if(t[now].data==v){
			splay(now,root);
			return now;
		}
		nxt=t[now].data<v?1:0;
		now=t[now].s[nxt];
	}
	return 0;
}
void destroy(int x){//删除节点 
	t[x].cnt=t[x].data=t[x].f=t[x].sum=t[x].s[0]=t[x].s[1]=0;
	if(x==size)size--;
}
void pop(int v){
	int node=find(v);
	if(!node)return;
	tot--;
	if(t[node].cnt>1){
		t[node].cnt--,t[node].sum--;
		return;
	}
	if(!t[node].s[0])root=t[node].s[1],t[root].f=0;
	else{
		int mx=t[node].s[0],B=t[node].s[1];
		while(t[mx].s[1])mx=t[mx].s[1];//找到左子树最大点，翻上来 
		splay(mx,root);
		connect(B,mx,1);
		connect(mx,0,1);
		upd(mx);
	}
	destroy(node);
}
int rank(int v){
	int ans=0,now=root,nxt;
	while(now){
		if(t[now].data==v){
			int r=ans+t[t[now].s[0]].sum+1;
			splay(now,root);
			return r;
		}
		nxt=t[now].data<v?1:0;
		if(nxt)ans+=t[t[now].s[0]].sum+t[now].cnt;
		now=t[now].s[nxt];
	}
	return ans;
}
int value(int x){
	int now=root,les,res;
	while(1){
		les=t[t[now].s[0]].sum;
		res=les+t[now].cnt;
		if(res<x)now=t[now].s[1],x-=res;
		else if(les>=x)now=t[now].s[0];
		else{splay(now,root);return t[now].data;}
	}
}
int upper(int v){
	int now=root,res=1e9;
	while(now){
		if(t[now].data>v&&t[now].data<res)res=t[now].data;
		now=t[now].s[t[now].data>v?0:1];
	}
	return res;
}
int lower(int v){
	int now=root,res=-1e9;
	while(now){
		if(t[now].data<v&&t[now].data>res)res=t[now].data;
		now=t[now].s[t[now].data>=v?0:1];//注意大于等于 
	}
	return res;
}
int main(){
	int i,n,opt,num;
	scanf("%d",&n);
	for(i=0;i<n;i++){
		scanf("%d%d",&opt,&num);
		if(opt==1)push(num);//插入num
		else if(opt==2)pop(num);//移除（一个）num，不存在则不变
		else if(opt==3)printf("%d\n",rank(num));//查询num的排名（比num小的个数+1）
		else if(opt==4)printf("%d\n",value(num));//查询排名num的数字
		else if(opt==5)printf("%d\n",lower(num));//查询最大的小于x的数
		else printf("%d\n",upper(num));//查询最小的大于x的数
	}
	return 0;
}
```

也可以在$Splay$上做一些文章，比如拿每一个节点代表一个区间，类似线段树一样打标记更新。

例如下面这个题：初始序列$1-n$，每次翻转一个区间$[l,r]$。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define root t[0].s[1]
struct Tree{
	int s[2],f,rev,sum,v;
}t[200010];
int n,a[200010],tot;
void update(int x){
	t[x].sum=t[t[x].s[0]].sum+t[t[x].s[1]].sum+1;
}
void pushdown(int x){
	if(t[x].rev){
		if(t[x].s[0])t[t[x].s[0]].rev^=1;
		if(t[x].s[1])t[t[x].s[1]].rev^=1;
		swap(t[x].s[1],t[x].s[0]);
		t[x].rev=0;
	}
}
int build(int fa,int l,int r){
	int mid=((l+r)>>1),now=++tot;
	if(l>r)return 0;
	t[now].f=fa;
	t[now].sum=1;
	t[now].v=a[mid];
	if(l==r)return now;
	t[now].s[0]=build(now,l,mid-1);
	t[now].s[1]=build(now,mid+1,r);
	update(now);
	return now;
}
void connect(int x,int fa,int son){
	t[x].f=fa;
	t[fa].s[son]=x;
}
int id(int x){
	return x==t[t[x].f].s[1];
}
void rotate(int x){
	int idx=id(x);
	int y=t[x].f,B=t[x].s[idx^1],R=t[y].f;
	int idy=id(y);
	connect(B,y,idx);
	connect(y,x,idx^1);
	connect(x,R,idy);
	update(x);
	update(y);
}
void splay(int now,int to){
	to=t[to].f;
	while(to!=t[now].f){
		int up=t[now].f;
		if(to==t[up].f)rotate(now);
		else if(id(up)==id(now)){
			rotate(up);
			rotate(now);
		}else{
			rotate(now);
			rotate(now);
		}
	}
}
int find(int x){
	int now=root;
	while(1){
		pushdown(now);
		int ls=t[t[now].s[0]].sum;
		if(x<=ls)now=t[now].s[0];
		else if(x>ls+1)now=t[now].s[1],x-=ls+1;
		else return now;
	}
}
void reverse(int l,int r){
	int x=find(l),y=find(r+2);
	splay(x,root);
	splay(y,t[x].s[1]);
	t[t[t[x].s[1]].s[0]].rev^=1;
}
void print(int x){
	pushdown(x);
	//printf("%d:%d %d\n",x,t[x].s[0],t[x].s[1]);
	if(t[x].s[0])print(t[x].s[0]);
	if(t[x].v<1e8&&t[x].v>0)printf("%d ",t[x].v-1);
	if(t[x].s[1])print(t[x].s[1]);
}
int main(){
	int i,q,l,r;
	scanf("%d%d",&n,&q);
	for(i=2;i<=n+1;i++)a[i]=i;
	a[1]=-1e9;a[n+2]=1e9;
	root=build(0,1,n+2);
	for(i=0;i<q;i++){
		scanf("%d%d",&l,&r);
		reverse(l,r);
	}
	print(root);
	return 0;
}
```

