## Lucas定理

求
$$
C_{n+m}^m\,mod\,p,p\in Prime
$$

```c++
#include <bits/stdc++.h>
const int maxn = 1e5+23;
typedef long long ll;
using namespace std;
int T,n,m,p;
ll a[maxn],b[maxn];
ll lucas(int x,int y)
{
    if(x<y) return 0;
    else if(x<p) return b[x]*a[y]*a[x-y]%p;
    else return lucas(x/p,y/p)*lucas(x%p,y%p)%p;
}
int main()
{
    scanf("%d",&T);
    while(T--){
        scanf("%d%d%d",&n,&m,&p);
        a[0]=a[1]=b[0]=b[1]=1;
        for(int i=2;i<=n+m;i++) b[i]=b[i-1]*i%p;
        for(int i=2;i<=n+m;i++) a[i]=(p-p/i)*a[p%i]%p;
        for(int i=2;i<=n+m;i++) a[i]=a[i-1]*a[i]%p;
        printf("%lld\n",lucas(n+m,m));
    }
    return 0;
}

```

## exLucas

$$
C_n^m\,mod\,p,p\notin Prime
$$

```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
void exgcd(ll a,ll b,ll &x,ll &y){
    if (!b) return (void)(x=1,y=0);
    exgcd(b,a%b,x,y);
    ll tmp=x;x=y;y=tmp-a/b*y;
}
ll gcd(ll a,ll b){
    if (b==0) return a;
    return gcd(b,a%b);
}
ll INV(ll a,ll p){
    ll x,y;
    exgcd(a,p,x,y);
    return (x+p)%p;
}
ll lcm(ll a,ll b){
    return a/gcd(a,b)*b;
}
ll mabs(ll x){
    return (x>0?x:-x);
}
ll fast_mul(ll a,ll b,ll p){
    ll t=0;a%=p;b%=p;
    while (b){
        if (b&1LL) t=(t+a)%p;
        b>>=1LL;a=(a+a)%p;
    }
    return t;
}
ll fast_pow(ll a,ll b,ll p){
    ll t=1;a%=p;
    while (b){
        if (b&1LL) t=(t*a)%p;
        b>>=1LL;a=(a*a)%p;
    }
    return t;
}
ll F(ll n,ll P,ll PK){
    if (n==0) return 1;
    ll rou=1;//循环节
    ll rem=1;//余项
    for (ll i=1;i<=PK;i++){
        if (i%P) rou=rou*i%PK;
    }
    rou=fast_pow(rou,n/PK,PK);
    for (ll i=PK*(n/PK);i<=n;i++){
        if (i%P) rem=rem*(i%PK)%PK;
    }
    return F(n/P,P,PK)*rou%PK*rem%PK;
}
ll G(ll n,ll P){
    if (n<P) return 0;
    return G(n/P,P)+(n/P);
}
ll C_PK(ll n,ll m,ll P,ll PK){
    ll fz=F(n,P,PK),fm1=INV(F(m,P,PK),PK),fm2=INV(F(n-m,P,PK),PK);
    ll mi=fast_pow(P,G(n,P)-G(m,P)-G(n-m,P),PK);
    return fz*fm1%PK*fm2%PK*mi%PK;
}
ll A[1001],B[1001];
//x=B(mod A)
ll exLucas(ll n,ll m,ll P){
    ll ljc=P,tot=0;
    for (ll tmp=2;tmp*tmp<=P;tmp++){
        if (!(ljc%tmp)){
            ll PK=1;
            while (!(ljc%tmp)){
                PK*=tmp;ljc/=tmp;
            }
            A[++tot]=PK;B[tot]=C_PK(n,m,tmp,PK);
        }
    }
    if (ljc!=1){
        A[++tot]=ljc;B[tot]=C_PK(n,m,ljc,ljc);
    }
    ll ans=0;
    for (ll i=1;i<=tot;i++){
        ll M=P/A[i],T=INV(M,A[i]);
        ans=(ans+B[i]*M%P*T%P)%P;
    }
    return ans;
}
ll n,m,P;
int main(){
    cin >> n >> m >> P;
    printf("%lld",exLucas(n,m,P));
}

```

