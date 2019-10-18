# Miller-Rabin素数测试

```c++
#include <bits/stdc++.h>
using namespace std;

long long fpow(long long x, long long y, long long p) {
    x %= p;
    y %= p;
    return (x * y - (long long)((long double)x / p * y + .5) * p + p) % p;
}

long long _pow(long long x, long long n, long long mod) {
    long long result = 1;
    for (; n; n >>= 1, x = fpow(x, x, mod)) {
        if (n & 1) {
            result = fpow(result, x, mod);
        }
    }
    return result;
}

long long f[] = { 2, 3, 13, 17, 61, 19260817 };
bool check(long long x) {
    if (x <= 1) {
        return false;
    }
    for (int i = 0; i <= 5; ++i) {
        if (x == f[i]) {
            return true;
        } else if (x % f[i] == 0) {
            return false;
        }
    }
    for (int i = 0; i <= 5; ++i) {
        long long tmp = x - 1;
        while (!(tmp & 1)) {
            tmp >>= 1;
        }
        long long s = _pow(f[i], tmp, x);
        while (s != x - 1 && s != 1 && tmp != x - 1) {
            tmp <<= 1;
            s = fpow(s, s, x);
        }
        if (s != x - 1 && !(tmp & 1)) {
            return false;
        }
    }
    return true;
}

int main() {
    long long n;cin >> n >> n;
    while (~scanf("%lld", &n)) {
        puts(check(n) ? "Yes" : "No");
    }
    return 0;
}

```



## Miller-Rabin+Pollard-Rho分解

```c++
#include <bits/stdc++.h>
using namespace std;
const int Times = 10;
const int N = 5500;
const int mod = 998244353;
typedef long long ll;

ll ct,cnt;
ll fac[N],num[N];

ll gcd(ll a,ll b)
{
    return b==0 ? a : gcd(b,a%b);
}

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

ll multi(ll a,ll b,ll m)
{
    ll ans=0;
    a%=m;
    while(b){
        if(b&1){
            ans=(ans+a)%m;
            b--;
        }
        b>>=1;
        a=(a+a)%m;
    }
    return ans;
}

ll quick_mod(ll a,ll b,ll m)
{
    ll ans=1;a%=m;
    while(b){
        if(b&1){
            ans=multi(ans,a,m);
            b--;
        }
        b>>=1;
        a=multi(a,a,m);
    }
    return ans;
}

bool Miller_Rabin(ll n)
{
    if(n==2) return true;
    if(n<2||!(n&1)) return false;
    ll m=n-1;int k=0;
    while((m&1)==0){
        k++;m>>=1;
    }
    for(int i=0;i<Times;i++){
        ll a=rand()%(n-1)+1;ll x=quick_mod(a,m,n);ll y=0;
        for(int j=0;j<k;j++){
            y=multi(x,x,n);
            if(y==1&&x!=1&&x!=n-1) return false;
            x=y;
        }
        if(y!=1) return false;
    }
    return true;
}

ll pollard_rho(ll n,ll c)
{
    ll i=1,k=2,x=rand()%(n-1)+1;ll y=x;
    while(1){
        i++;
        x=(multi(x,x,n)+c)%n;
        ll d=gcd((y-x+n)%n,n);
        if(d>1&&d<n) return d;
        if(y==x) return n;
        if(i==k){y=x;k<<=1;}
    }
}

void find(ll n, ll c)
{
    if(n==1) return ;
    if(Miller_Rabin(n)){
        fac[ct++]=n;
        return ;
    }
    ll p=n,k=c;
    while(p>=n) p=pollard_rho(p,c--);
    find(p,k);
    find(n/p,k);
}


int main()
{
    ll n;
    srand(time(NULL));
    while(cin>>n){
        ct=0;
        find(n,120);
        sort(fac,fac+ct);
        num[0]=1;int k=1;
        for(int i=1;i<ct;i++){
            if(fac[i]==fac[i-1]) num[k-1]++;
            else{
                num[k]=1;fac[k++]=fac[i];
            }
        }
        cnt=k;
    }
    return 0;
}

```

