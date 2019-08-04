## Manacher

注意开两倍数组！

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define N 11000010
char c[N<<1],a[N];
int len[N<<1];
void manacher(char a[]){
	int i,mr=0,mid,ans=0;
	c[0]='.';
	for(i=0;a[i];i++)c[i*2+1]='#',c[i*2+2]=a[i];
	c[i*2+1]='#';
	for(i=0;c[i];i++){
		if(i<mr)len[i]=min(mr-i,len[mid*2-i]);
		else len[i]=1;
		while(c[i+len[i]]==c[i-len[i]])len[i]++;
		if(i+len[i]>mr)mr=i+len[i],mid=i;
		ans=max(ans,len[i]-1);
	}
	printf("%d",ans);
}
int main(){
	scanf("%s",a);
	manacher(a);
	return 0;
}
```

