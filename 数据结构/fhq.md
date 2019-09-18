## FHQ Treap

```cpp
#include<bits/stdc++.h>
using namespace std;
#define root t[0].s[1]
#define ls t[x].s[0]
#define rs t[x].s[1]
struct fhq{
	int size,key,val,s[2];
}t[100010<<1];
int tot;
void upd(int x){
	t[x].size=t[ls].size+t[rs].size+1;
}
void split(int x,int k,int &a,int &b){
	if(!x){a=b=0;return;}
	if(t[x].val<=k)a=x,split(rs,k,rs,b);
	else b=x,split(ls,k,a,ls);
	upd(x);
}
int merge(int x,int y){
	if(!x||!y)return x+y;
	if(t[x].key<t[y].key){rs=merge(rs,y),upd(x);return x;}
	else{t[y].s[0]=merge(x,t[y].s[0]),upd(y);return y;}
}
int newnode(int v){
	t[++tot].key=rand();
	t[tot].val=v;
	t[tot].size=1;
	return tot;
}
void ins(int v){
	int x,y;
	split(root,v,x,y);
	root=merge(merge(x,newnode(v)),y);
}
void del(int v){
	int x,y,z;
	split(root,v,x,y);
	split(x,v-1,x,z);
	z=merge(t[z].s[0],t[z].s[1]);
	root=merge(x,merge(z,y));
}
void rank(int x){
	int a,b;
	split(root,x-1,a,b);
	printf("%d\n",t[a].size+1);
	root=merge(a,b);
}
void val(int x,int v){
	while(1){
		if(v<=t[ls].size)x=ls;
		else if(v==t[ls].size+1){
			printf("%d\n",t[x].val);
			return;
		}
		else v-=t[ls].size+1,x=rs;
	}
}
void pre(int v){
	int x,y;
	split(root,v-1,x,y);
	val(x,t[x].size);
	root=merge(x,y);
}
void suc(int v){
	int x,y;
	split(root,v,x,y);
	val(y,1);
	root=merge(x,y);
}
int main(){
	int n,opt,num;
	scanf("%d",&n);
	while(n--){
		scanf("%d%d",&opt,&num);
		if(opt==1)ins(num);
		else if(opt==2)del(num);
		else if(opt==3)rank(num);
		else if(opt==4)val(root,num);
		else if(opt==5)pre(num);
		else suc(num);
	}
	return 0;
}
```



```cpp
#include<bits/stdc++.h>
using namespace std;
#define ls t[x].s[0]
#define rs t[x].s[1]
struct FHQ{
	int s[2],val,key,size,rev;
}t[100010];
int n,m,tot,root;
void upd(int x){
	t[x].size=t[ls].size+t[rs].size+1;
}
int newnode(int v){
	t[++tot].key=rand();t[tot].size=1;t[tot].val=v;
	return tot;
}
void pushdown(int x){
	if(t[x].rev){
		if(ls)t[ls].rev^=1;
		if(rs)t[rs].rev^=1;
		swap(ls,rs);
		t[x].rev=0;
	}
}
void split(int x,int k,int &a,int &b){//以x为根的子树，分裂成左边k个
	if(!x){a=b=0;return;}
	pushdown(x);
	if(t[ls].size>=k)b=x,split(ls,k,a,ls),upd(x);
	else a=x,split(rs,k-t[ls].size-1,rs,b),upd(x);
}
int merge(int x,int y){//合并x,y为根的子树
	if(!x||!y)return x+y;
	pushdown(x);pushdown(y);
	if(t[x].key<t[y].key){
		rs=merge(rs,y);upd(x);return x;
	}else{
		t[y].s[0]=merge(x,t[y].s[0]);upd(y);return y;
	}
}
void rev(int l,int r){
	int x,y,z;
	split(root,r,x,y);
	split(x,l-1,x,z);
	t[z].rev=1;
	root=merge(merge(x,z),y);
}
void print(int x){
	pushdown(x);
	if(ls)print(ls);
	printf("%d ",t[x].val);
	if(rs)print(rs);
}
int main(){
	int i;
	scanf("%d%d",&n,&m);
	for(i=1;i<=n;i++)
		root=merge(root,newnode(i));
	for(i=0;i<m;i++){
		int l,r;
		scanf("%d%d",&l,&r);
		rev(l,r);
	}
	print(root);
	return 0;
}
```

题目和$Splay$里面的一样。