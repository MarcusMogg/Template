## 后缀排序

模板

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
#define N 1000010
char s[N];
int n,sa[N],c[N],x[N],y[N];
void getsa(int m){
	int i,k;
	for(i=0;i<=m;i++)c[i]=0;
	for(i=1;i<=n;i++)c[x[i]=s[i]]++;//also x[i]=s[i]-'a'
	for(i=1;i<=m;i++)c[i]+=c[i-1];
	for(i=n;i;i--)sa[c[x[i]]--]=i;
	for(k=1;k<=n;k<<=1){
		int p=0;
		for(i=n-k+1;i<=n;i++)y[++p]=i;
		for(i=1;i<=n;i++)if(sa[i]>k)y[++p]=sa[i]-k;
		for(i=0;i<=m;i++)c[i]=0;
		for(i=1;i<=n;i++)c[x[y[i]]]++;
		for(i=1;i<=m;i++)c[i]+=c[i-1];
		for(i=n;i;i--)sa[c[x[y[i]]]--]=y[i];
		swap(x,y);
		p=x[sa[1]]=1;
		for(i=2;i<=n;i++)
			x[sa[i]]=y[sa[i]]==y[sa[i-1]]&&y[sa[i]+k]==y[sa[i-1]+k]?p:++p;
		if(p>=n)break;
		m=p;
	}
}
int h[N],rnk[N];
void geth(){
	int i,j,k=0;
	for(i=1;i<=n;i++)rnk[sa[i]]=i;
	for(i=1;i<=n;i++){
		if(k)k--;
		j=sa[rnk[i]-1];
		while(s[i+k]==s[j+k])k++;
		h[rnk[i]]=k;
	}
}
int main(){
	int i;
	scanf("%s",s+1);
	n=strlen(s+1);
	getsa(260);
	geth();
	for(i=1;i<=n;i++)printf("%d ",sa[i]);
	return 0;
}
```

$$lcp(i,j)$$：$$suf(sa[i]),suf(sa[j])$$的最长公共前缀（$$sa[i]:$$排名$$i$$的后缀，其最前端的位置）

$$\quad lcp(i,j)=lcp(j,i)$$

$$\quad lcp(i,i)=len(sa[i])=n-sa[i]+1$$

显然，$$lcp(i,k)=min(height[j])$$，可用$$ST$$表求出。

$$height[i]$$：$$lcp(sa[i],sa[i-1])$$

$$H[i]:height[rank[i]]$$，有$$H[i]\geq H[i-1]-1$$，$$geth$$可求出（$$k$$即为$$H[i],h[i]$$即为$$height[i]$$）

本质不同子串：$$\frac{n\times(n-1)}2-\sum_{i=1}^nheight[i]$$（对于每个后缀，有$$height[i]$$个之前出现过）

改为求区间不同：$$HDU6422$$，把$$height$$看做$$LCP$$，多个区间求即可。

