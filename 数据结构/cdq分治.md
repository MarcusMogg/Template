## CDQ分治

常见$CDQ$分治：将一个问题转化为三维偏序问题后，将第一维排序，按照第二维归并排序，在第三维用$BIT$计数。

模板：三维偏序（计算$a_i\leq a_j,b_i\leq b_j,c_i\leq c_j,i\leq j$的对数）

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
#define N 200010
int n,k,bit[N];
void add(int p,int x){while(p&&p<N)bit[p]+=x,p+=p&(-p);}
int query(int p){
	int ans=0;
	while(p)ans+=bit[p],p-=p&(-p);
	return ans;
}
struct Point{
	int a,b,c,ans;
	int num;//cdq只能左贡献右，故使用去重
	bool operator<(const Point&p)const{return a<p.a||a==p.a&&(b<p.b||b==p.b&&c<p.c);}
}a[N],tmp[N];
int cmp(Point a,Point b){
	return a.b==b.b?a.c<b.c:a.b<b.b;
}
int ans[N];
void dc(int l,int r){
	int i,L,R,mid=(l+r)>>1;
	if(l==r)return;
	dc(l,mid);dc(mid+1,r);
	int now=0;
	L=l,R=mid+1;
	while(R<=r){
		while(L<=mid&&a[L].b<=a[R].b){
			tmp[now++]=a[L];
			add(a[L].c,a[L].num),L++;
		}
		a[R].ans+=query(a[R].c);
		tmp[now++]=a[R];
		R++;
	}
	for(i=l;i<L;i++)add(a[i].c,-a[i].num);
	while(L<=mid)tmp[now++]=a[L++];
	for(i=l;i<=r;i++)a[i]=tmp[i-l];
}
int main(){
	int i;
	scanf("%d%d",&n,&k);
	for(i=1;i<=n;i++)scanf("%d%d%d",&a[i].a,&a[i].b,&a[i].c),a[i].num=1;
	sort(a+1,a+1+n);
	int last=1,tot=1;
	for(i=2;i<=n;i++){
		if(a[i].a==a[last].a&&a[i].b==a[last].b&&a[i].c==a[last].c)a[last].num++;
		else{
			a[++tot]=a[i];
			last=tot;
		}
	}
	dc(1,tot);
	for(i=1;i<=tot;i++)ans[a[i].ans+a[i].num-1]+=a[i].num;
	for(i=0;i<n;i++)printf("%d\n",ans[i]);
	return 0;
}
```

