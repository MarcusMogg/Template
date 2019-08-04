## KMP

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define N 1000010
char a[N],b[N];
int fail[N];
void kmp(char a[],char b[],int la,int lb){//a include b
	int i,j=0;
	//fail[i]: failed to match b[i], then try to match fail[i]
	//b[0...fail[i]-1]=b[i-fail[i]...i-1]
	
	//a,b:0-index
	for(i=1;i<lb;i++){
		int p=fail[i];
		while(b[i]!=b[p]&&p)p=fail[p];
		if(b[p]==b[i])fail[i+1]=p+1;
		else fail[i+1]=0;
	}
	j=0;
	for(i=0;i<la;i++){
		while(a[i]!=b[j]&&j)j=fail[j];
		if(a[i]==b[j])j++;
		if(j==lb){
			printf("%d\n",i-lb+2);
			//matching starting, index 1
			j=fail[j];
		}
	}
	for(i=1;i<=lb;i++)printf("%d ",fail[i]);
}
int main(){
	scanf("%s%s",a,b);
	kmp(a,b,strlen(a),strlen(b));
	return 0;
}
```

