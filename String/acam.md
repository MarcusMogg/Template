## AC自动机

多个$$T$$串，统计分别在$$S$$串中出现次数

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
#define N 2000010
struct ACAM{
	int tr[N][26],tot;
	int fail[N],num[N],now,sign[N];
	int q[N],hd,tl;
	void insert(int x,char s[]){
		int i;
		now=0;
		for(i=0;s[i];i++){
			int p=s[i]-'a';
			if(!tr[now][p])tr[now][p]=++tot;
			now=tr[now][p];
		}
		sign[x]=now;//instead of sign[now]=x!
	}
	void build(){
		int i;
		for(i=0;i<26;i++)if(tr[0][i])q[++tl]=tr[0][i];
		while(hd<tl){
			int p=q[++hd];
			for(i=0;i<26;i++){
				if(tr[p][i])fail[tr[p][i]]=tr[fail[p]][i],q[++tl]=tr[p][i];
				else tr[p][i]=tr[fail[p]][i];
			}
		}
	}
	void jud(char s[]){
		int i;
		now=0;
		for(i=0;s[i];i++)now=tr[now][s[i]-'a'],num[now]++;
		for(i=tl;i;i--)num[fail[q[i]]]+=num[q[i]];
        //toposort, add from leaves to root
	}
}T;
char s[N],t[N];
int main(){
	int i,n;
	scanf("%d",&n);
	for(i=0;i<n;i++)scanf("%s",t),T.insert(i,t);
	T.build();
	scanf("%s",s);
	T.jud(s);
	for(i=0;i<n;i++)printf("%d\n",T.num[T.sign[i]]);
	return 0;
}
```

$$UVA Live4126$$ $$AC$$自动机套状压$$DP$$：$$dp[i][len][state]$$：到$$i$$节点，长度为$$len$$，状态为$$state$$。从后往前$$dp$$，转移方程$$dp[i][len][state]=\sum dp[tr[i][k]][len+1][state|ed[tr[i][k]]]$$。注意在更新$$fail$$的时候更新$$ed$$状态。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
#define N 105
char a[25];
int n,m,trie[N][26],fail[N],sign[N],tot;
void add(int x){
	int now=0,i;
	for(i=0;a[i];i++){
		int p=a[i]-'a';
		if(!trie[now][p])trie[now][p]=++tot;
		now=trie[now][p];
	}
	sign[now]|=1<<x;
}
queue<int>Q;
void build(){
	int i,pos;
	for(i=0;i<26;i++)if(trie[0][i])Q.push(trie[0][i]);
	while(!Q.empty()){
		pos=Q.front();Q.pop();
		for(i=0;i<26;i++){
			if(trie[pos][i]){
				fail[trie[pos][i]]=trie[fail[pos]][i];
				Q.push(trie[pos][i]);
				sign[trie[pos][i]]|=sign[fail[trie[pos][i]]];
			}else trie[pos][i]=trie[fail[pos]][i];
		}
	}
}
ll f[27][N][1030];//f[len][pos][state]
ll dp(int len,int state,int pos){
	ll &ans=f[len][pos][state];
	if(~ans)return ans;
	if(len==n)return ans=state==(1<<m)-1;
	int i;
	ans=0;
	for(i=0;i<26;i++)
		ans+=dp(len+1,state|sign[trie[pos][i]],trie[pos][i]);
	return ans;
}
char out[30];
void print(int len,int state,int pos){
	if(len==n){
		fwrite(out,sizeof(out[0]),n,stdout);
		puts("");
		return;
	}
	int i;
	for(i=0;i<26;i++){
		out[len]=i+'a';
		if(f[len+1][trie[pos][i]][state|sign[trie[pos][i]]]>0)
			print(len+1,state|sign[trie[pos][i]],trie[pos][i]);
	}
}
int main(){
	int i,t=0;
	while(~scanf("%d%d",&n,&m)&&(n|m)){
		t++;
		memset(trie,0,sizeof(trie));
		memset(fail,0,sizeof(fail));
		memset(f,-1,sizeof(f));
		memset(sign,0,sizeof(sign));
		tot=0;
		for(i=0;i<m;i++)scanf("%s",a),add(i);
		build();
		ll ans=dp(0,0,0);
		printf("Case %d: %lld suspects\n",t,ans);
		if(ans<=42)print(0,0,0);
	}
	return 0;
}
```

