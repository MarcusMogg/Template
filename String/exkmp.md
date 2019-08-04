## ExKMP

求出$$b$$与$$a$$每一个后缀的最长公共前缀$$ext[i]$$

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define N 100010
char a[N],b[N];
int la,lb;
int ext[N],nxt[N];
void getnxt(char t[]){
	int i=0,p0=1;
	nxt[0]=lb;
	while(t[i]==t[i+1]&&i<lb)i++;
	nxt[1]=i;
	for(i=2;i<lb;i++){
		if(i+nxt[i-p0]<nxt[p0]+p0)nxt[i]=nxt[i-p0];
		else{
			int j=max(0,nxt[p0]+p0-i);
			while(t[j]==t[i+j]&&i+j<lb)j++;
			nxt[i]=j;p0=i;
		}
	}
}
void exkmp(char s[],char t[]){
	//s,t:0-index
	//ext[i]:s[i...i+ext[i]-1]=t[0...ext[i]-1]
	//nxt[i]:t[0...nxt[i]-1]=t[i...i+nxt[i]-1]
	getnxt(t);
	int i=0,p0=0;
	while(s[i]==t[i]&&i<min(la,lb))i++;
	ext[0]=i;
	for(i=1;i<la;i++){
		//p0......k k+1..k+l....p
		//0..l-1..a b....b+l-1
		//t[0...l-1]=t[b...b+l-1]
		//t[b..b+l-1]=s[k+1...k+l]
		//k+1:i b:i-p0 l:nxt[b] p:ext[p0]+p0
		if(i+nxt[i-p0]<ext[p0]+p0)ext[i]=nxt[i-p0];
		else{
			//p0...........k k+1...p...k+l
			//0.......l-1..a b.......b+l-1
			//s[k+1...p]=t[b..b+(p-k-1)]=t[0...p-k-1]
			//brute force matching, update p0
			int j=max(0,ext[p0]+p0-i);
			while(t[j]==s[i+j]&&j<lb&&i+j<la)j++;
			ext[i]=j;p0=i;
		}
	}
}
int main(){
	int i;
	scanf("%s%s",a,b);
	la=strlen(a);lb=strlen(b);
	exkmp(a,b);
	for(i=0;i<lb;i++)printf("%d ",nxt[i]);
	puts("");
	for(i=0;i<la;i++)printf("%d ",ext[i]);
	return 0;
}
```

