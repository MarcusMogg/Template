## BSGS

求解关于$x$的方程
$$
y^x=z(mod\,p)\\(y,p)=1
$$

```c++
#include <bits/stdc++.h>
#define N 7679977
#define hash HASH
using namespace std;
typedef long long ll;

ll qpow(long long int a,long long b)
{
    ll ans=1,base=a;
    while(b!=0){
        if(b&1!=0) ans*=base;
        base*=base;
        b>>=1;
        base%=mod;
        ans%=mod;
    }
    return ans;
}

class Hash{
    static const int HASHMOD = 7679977;
    int top,hash[HASHMOD+100],value[N+100],stack[1<<16];
    int locate(const int x) const{
        int h=x%N;
        while(hash[h]!=-1&&hash[h]!=x) h++;
        return h;
    }
    public :
    Hash():top(0) {memset(hash,0xff,sizeof(hash));}
    void insert(const int x,const int v){
        const int h=locate(x);
        if(hash[h]==-1) hash[h]=x,value[h]=v,stack[++top]=h;
    }
    int get(const int x) const{
        const int h=locate(x);
        return hash[h]==x? value[h]:-1;
    }
    void clear(){
        while(top) hash[stack[top--]]=-1;
    }
}hash;

struct Triple
{
    ll x,y,z;
    Triple(const ll a,const ll b,const ll c) :x(a),y(b),z(c) {}
};

Triple ExtendedGCD(const ll a,const ll b)
{
    if(b==0) return Triple(1,0,a);
    const Triple last=ExtendedGCD(b,a%b);
    return Triple(last.y,last.x-a/b*last.y,last.z);
}

int A,B,C;
ll Babystep(ll A,ll B,ll C)
{
    const int sqrtn=static_cast<int>(ceil(sqrt(C)));
    ll base=1;
    hash.clear();
    for(int i=0;i<sqrtn;i++){
        hash.insert(base,i);
        base=base*A%C;
    }
    ll i=0,j=-1,D=1;
    for(;i<sqrtn;i++){
        Triple res=ExtendedGCD(D,C);
        const int c=C/res.z;
        res.x=(res.x*B/res.z%c+c)%c;
        j=hash.get(res.x);
        if(j!=-1) return i*sqrtn+j;
        D=D*base%C;
     }
     return -1;
}

int main()
{
    cin >> A >> B >> C ;        //A^x==B(modC)
    const ll ans=Babystep(A,B,C);
    printf("%I64d\n",ans);
}

```

## 拓展BSGS

对$(y,p)\neq1$

令$d=gcd(y,p)$，将方程改写为等式
$$
y^x+kp=z
$$

$$
\frac{y}{d}y^{x-1}+k\frac{p}{d}=\frac{z}{d}
$$

不断重复上步，直到互质

方程的形式变为
$$
\frac{y^k}{\prod_{i=1}^k{d_i}}y^{x-k}=\frac{z}{\prod_{i=1}^k{d_i}}(mod\,\frac{p}{\prod_{i=1}^k{d_i}})
$$
枚举$0<x<k$，对于$x\geq k$ ，使用BSGS算法求解。







