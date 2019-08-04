## 回文自动机

多组数据不必初始化

$$fail$$：最长回文后缀位置

$$tr$$：向两边扩展一个字符得到孩子

$$len$$：节点代表回文串长度

$$tot-2$$：不同回文串个数

$$cnt$$：该回文串在整个串中出现次数

$$num$$：$$fail$$路径深度，$$i$$结尾回文串数目

$$0$$：偶数长度的根 $$\quad 1$$：奇数长度的根

```cpp
struct PT{
	int tr[N][P],fail[N],len[N];
    int cnt[N],num[N];
	int tot,s[N],n,last,i;
	int newnode(int l){
		for(i=0;i<P;i++)tr[tot][i]=0;
		len[tot]=l;
        cnt[tot]=0;
        num[tot]=1;
		return tot++;
	}
	void init(){
		n=last=tot=0;
		newnode(0);newnode(-1);
		s[0]=-1;
		fail[0]=1;
	}
	int getfail(int p){
		while(s[n-len[p]-1]!=s[n])p=fail[p];
		return p;
	}
	void add(int p){
		s[++n]=p;
		int cur=getfail(last);
		if(!tr[cur][p]){
			int now=newnode(len[cur]+2);
			int getf=getfail(fail[cur]);
			fail[now]=tr[getf][p];
			tr[cur][p]=now;
            num[now]=num[fail[now]]+1;
		}
		last=tr[cur][p];
        cnt[last]++;
	}
	void insert(char a[]){
		int i;
		for(i=0;a[i];i++)add(a[i]-'a');
	}
    void count(){
		for(i=tot-1;i>=0;i--)cnt[fail[i]]+=cnt[i];
    }
}pam;
```

