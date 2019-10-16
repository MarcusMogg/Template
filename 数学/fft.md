## FFT

1. 简单$FFT$

```CPP
#include<bits/stdc++.h>
struct complex{
	double x,y;
	complex(double xx=0,double yy=0){x=xx;y=yy;}
	complex operator+(const complex a)const{return {x+a.x,y+a.y};}
	complex operator-(const complex a)const{return {x-a.x,y-a.y};}
	complex operator*(const complex a)const{
        return {x*a.x-y*a.y,x*a.y+y*a.x};
    }
};
#define N 2100010
complex a[N],b[N],c[N],wn1,wnk,t;
int m,n,mx,limit;
int r[N];
void fft(complex *F,int sign){
	int i,j,len;
	for(i=0;i<limit;i++)
		if(i<r[i])std::swap(F[r[i]],F[i]);
	for(len=1;len<limit;len<<=1){
		wn1=complex(cos(acos(-1)/len),sign*sin(acos(-1)/len));
		for(j=0;j<limit;j+=(len<<1)){
			wnk=complex(1,0);
			for(i=j;i<j+len;i++){
				t=F[i+len]*wnk;
				F[i+len]=F[i]-t;
				F[i]=F[i]+t;
				wnk=wnk*wn1;
			}
		}
	}
	if(sign==-1)for(i=0;i<limit;i++)F[i].x/=limit;
}
int main(){
	int i,n,m;
	scanf("%d%d",&n,&m);
	for(i=0;i<=n;i++)scanf("%lf",&a[i].x);
	for(i=0;i<=m;i++)scanf("%lf",&b[i].x);
	mx=m+n,limit=1;
	while(limit<=mx)limit<<=1;
	for(i=0;i<=limit;i++)r[i]=(r[i/2]/2)|((i&1)?limit>>1:0);
	fft(a,1);fft(b,1);
	for(i=0;i<limit;i++)c[i]=a[i]*b[i];
	fft(c,-1);
	for(i=0;i<=mx;i++)printf("%.0f ",fabs(c[i].x));
	return 0;
}
```

2. 拆系数$FFT$（取模）

卷积之前注意mx,limit,fft\_prepare​

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define M 262144
struct comp{
	double x,y;
	comp():x(0),y(0){}
	comp(const double &_x,const double &_y):x(_x),y(_y){}
};
comp operator+(const comp &a,const comp &b){
    return comp(a.x+b.x,a.y+b.y);
}
comp operator-(const comp &a,const comp &b){
    return comp(a.x-b.x,a.y-b.y);
}
comp operator*(const comp &a,const comp &b){
    return comp(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);
}
comp conj(const comp &a){return comp(a.x,-a.y);}
double PI=acos(-1);
comp w[M+5];
int rev[M+5];
int A[M+5],B[M+5],C[M+5],lim,mx,mod;
void fft(comp *a,int n){
	int i,j,k,lyc;
	for(i=0;i<n;i++)if(i<rev[i]) swap(a[i],a[rev[i]]);
	for(i=2,lyc=n>>1;i<=n;i<<=1,lyc>>=1)
		for(j=0;j<n;j+=i){
			comp *l=a+j,*r=a+j+(i>>1),*p=w;
			for(k=0;k<(i>>1);k++){
				comp tmp=*r**p;
				*r=*l-tmp,*l=*l+tmp;
				++l,++r,p+=lyc;
			}
		}
}
void fft_prepare(){
	int i;
	for(i=0;i<lim;i++)rev[i]=rev[i>>1]>>1|((i&1)<<(mx-1));
	for(i=0;i<lim;i++)w[i]=comp(cos(2*PI*i/lim),sin(2*PI*i/lim));
}
comp a[M+5],b[M+5],ta[M+5],tb[M+5];
void conv(int *x,int *y,int *z){
	int i,j;
	for(i=0;i<lim;i++)(x[i]+=mod)%=mod,(y[i]+=mod)%=mod;
	for(i=0;i<lim;i++){
		ta[i]=comp(x[i]&32767,x[i]>>15);
		tb[i]=comp(y[i]&32767,y[i]>>15);
	}
	fft(ta,lim);fft(tb,lim);
	for(i=0;i<lim;i++){
		j=(lim-i)%lim;//0的特判
		comp da,db,dc,dd;
  		da=(ta[i]+conj(ta[j]))*comp(0.5,0);
		db=(ta[i]-conj(ta[j]))*comp(0,-0.5);
		dc=(tb[i]+conj(tb[j]))*comp(0.5,0);
		dd=(tb[i]-conj(tb[j]))*comp(0,-0.5);
		a[j]=da*dc+da*dd*comp(0,1);
		b[j]=db*dc+db*dd*comp(0,1);
	}
	fft(a,lim);fft(b,lim);
	for(i=0;i<lim;i++){
		ll da,db,dc,dd;
		da=(ll)(a[i].x/lim+0.5)%mod;
		db=(ll)(a[i].y/lim+0.5)%mod;
		dc=(ll)(b[i].x/lim+0.5)%mod;
		dd=(ll)(b[i].y/lim+0.5)%mod;
		z[i]=(da+((db+dc)<<15)+(dd<<30))%mod;
	}
}
int main(){
	int i,n,m;
	scanf("%d%d%d",&n,&m,&mod);
	n++;m++;
	for(i=0;i<n;i++)scanf("%d",&A[i]);
	for(i=0;i<m;i++)scanf("%d",&B[i]);
	while((1<<mx)<n+m-1)mx++;
	lim=1<<mx;
	fft_prepare();
	conv(A,B,C);
	for(i=0;i<n+m-1;i++)printf("%d ",(C[i]+mod)%mod);
	puts("");
	return 0;
}
```

3. 多项式除法（商+模，取模，用拆系数$FFT$）

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define M 524290
struct comp{
	double x,y;
	comp():x(0),y(0){}
	comp(const double &_x,const double &_y):x(_x),y(_y){}
};
comp operator+(const comp &a,const comp &b){return comp(a.x+b.x,a.y+b.y);}
comp operator-(const comp &a,const comp &b){return comp(a.x-b.x,a.y-b.y);}
comp operator*(const comp &a,const comp &b){return comp(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
comp conj(const comp &a){return comp(a.x,-a.y);}
double PI=acos(-1);
comp w[M+5];
int rev[M+5];
int lim,mx,mod;
void fft(comp *a,int n){
	int i,j,k,lyc;
	for(i=0;i<n;i++)if(i<rev[i]) swap(a[i],a[rev[i]]);
	for(i=2,lyc=n>>1;i<=n;i<<=1,lyc>>=1)
		for(j=0;j<n;j+=i){
			comp *l=a+j,*r=a+j+(i>>1),*p=w;
			for(k=0;k<(i>>1);k++){
				comp tmp=*r**p;
				*r=*l-tmp,*l=*l+tmp;
				++l,++r,p+=lyc;
			}
		}
}
void fft_prepare(){
	int i;
	for(i=0;i<lim;i++)rev[i]=rev[i>>1]>>1|((i&1)<<(mx-1));
	for(i=0;i<lim;i++)w[i]=comp(cos(2*PI*i/lim),sin(2*PI*i/lim));
}
void conv(int *x,int *y,int *z,int n,int m,int mod){
	int i,j;
	static comp ta[M+5],tb[M+5],a[M+5],b[M+5];
	static int X[M+5],Y[M+5];
	for(i=0;i<n;i++)X[i]=(x[i]+mod)%mod;
	for(;i<lim;i++)X[i]=0;
	for(i=0;i<m;i++)Y[i]=(y[i]+mod)%mod;
	for(;i<lim;i++)Y[i]=0;
	for(i=0;i<lim;i++){
		ta[i]=comp(X[i]&32767,X[i]>>15);
		tb[i]=comp(Y[i]&32767,Y[i]>>15);
	}
	fft(ta,lim);fft(tb,lim);
	for(i=0;i<lim;i++){
		j=(lim-i)%lim;//0的特判
		comp da,db,dc,dd;
  		da=(ta[i]+conj(ta[j]))*comp(0.5,0);
		db=(ta[i]-conj(ta[j]))*comp(0,-0.5);
		dc=(tb[i]+conj(tb[j]))*comp(0.5,0);
		dd=(tb[i]-conj(tb[j]))*comp(0,-0.5);
		a[j]=da*dc+da*dd*comp(0,1);
		b[j]=db*dc+db*dd*comp(0,1);
	}
	fft(a,lim);fft(b,lim);
	for(i=0;i<lim;i++){
		ll da,db,dc,dd;
		da=(ll)(a[i].x/lim+0.5)%mod;
		db=(ll)(a[i].y/lim+0.5)%mod;
		dc=(ll)(b[i].x/lim+0.5)%mod;
		dd=(ll)(b[i].y/lim+0.5)%mod;
		z[i]=(((da+((db+dc)<<15)+(dd<<30))%mod)+mod)%mod;
	}
}
ll qpow(ll x,int p){
	ll ans=1;
	for(;p;p>>=1,x=x*x%mod)if(p&1)ans=ans*x%mod;
	return ans;
}
void polyinv(int *A,int *ans,int n){
	static int B[2][M+5];
	int bas,cur,i;
	memset(B,0,sizeof(B));
	B[0][0]=qpow(A[0],mod-2);
	for(mx=2,bas=1,lim=4,cur=1;bas<n*2;mx++,lim<<=1,cur^=1,bas<<=1){
		fft_prepare();
		memset(B[cur],0,sizeof(B[cur]));
		for(i=0;i<bas;i++)B[cur][i]=(ll)B[cur^1][i]*2%mod;
		conv(B[cur^1],B[cur^1],B[cur^1],bas,bas,mod);
		conv(B[cur^1],A,B[cur^1],bas,bas,mod);
		for(i=0;i<bas;i++)B[cur][i]=((ll)B[cur][i]-B[cur^1][i]+mod)%mod;
	}
	for(i=0;i<n;i++)ans[i]=((ll)B[cur^1][i]+mod)%mod;
}
void polydiv(int *a,int *b,int *d,int *r,int n,int m){
	int p=1,t=n-m+1,i;
	static int A0[M+5],B0[M+5];
	int tmp=0;
	while(p<=t<<1)p<<=1,tmp++;
	fill(A0,A0+p,0);reverse_copy(b,b+m,A0);
	polyinv(A0,B0,t);
	fill(B0+t,B0+p,0);
	reverse_copy(a,a+n,A0);fill(A0+t,A0+p,0);
	mx=tmp;lim=1<<mx;fft_prepare();conv(A0,B0,A0,p,p,mod);
	reverse(A0,A0+t);
	copy(A0,A0+t,d);
	tmp=0,p=1;while(p<=n)p<<=1,tmp++;
	fill(A0+t,A0+p,0);
	mx=tmp;lim=1<<mx;fft_prepare();conv(A0,b,A0,t,m,mod);
	for(i=0;i<m;i++)r[i]=(((ll)a[i]-A0[i])%mod+mod)%mod;
	fill(r+m,r+p,0);
}
int F[M+5],G[M+5];
int D[M+5],R[M+5];
int main(){
	int i,n,m;
	mod=998244353;
	scanf("%d%d",&n,&m);
	n++;m++;
	for(i=0;i<n;i++)scanf("%d",&F[i]);
	for(i=0;i<m;i++)scanf("%d",&G[i]);
	polydiv(F,G,D,R,n,m);
	for(i=0;i<n-m+1;i++)printf("%d ",D[i]);
	puts("");
	for(i=0;i<m-1;i++)printf("%d ",R[i]);
	puts("");
	return 0;
}
```

4. 多项式求逆（$F(x)*G(x)=1(mod\space x^n) $）

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define M 524290
struct comp{
	double x,y;
	comp():x(0),y(0){}
	comp(const double &_x,const double &_y):x(_x),y(_y){}
};
comp operator+(const comp &a,const comp &b){return comp(a.x+b.x,a.y+b.y);}
comp operator-(const comp &a,const comp &b){return comp(a.x-b.x,a.y-b.y);}
comp operator*(const comp &a,const comp &b){return comp(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
comp conj(const comp &a){return comp(a.x,-a.y);}
double PI=acos(-1);
comp w[M+5];
int rev[M+5];
int A[M+5],B[2][M+5],C[M+5],lim,mx,mod;
void fft(comp *a,int n){
	int i,j,k,lyc;
	for(i=0;i<n;i++)if(i<rev[i]) swap(a[i],a[rev[i]]);
	for(i=2,lyc=n>>1;i<=n;i<<=1,lyc>>=1)
		for(j=0;j<n;j+=i){
			comp *l=a+j,*r=a+j+(i>>1),*p=w;
			for(k=0;k<(i>>1);k++){
				comp tmp=*r**p;
				*r=*l-tmp,*l=*l+tmp;
				++l,++r,p+=lyc;
			}
		}
}
void fft_prepare(){
	int i;
	for(i=0;i<lim;i++)rev[i]=rev[i>>1]>>1|((i&1)<<(mx-1));
	for(i=0;i<lim;i++)w[i]=comp(cos(2*PI*i/lim),sin(2*PI*i/lim));
}
comp a[M+5],b[M+5],ta[M+5],tb[M+5];
int X[M+5],Y[M+5];
void conv(int *x,int *y,int *z){
	int i,j;
	for(i=0;i<lim/2;i++)X[i]=x[i];
	for(i=0;i<lim/2;i++)Y[i]=y[i];
	for(i=0;i<lim;i++)(X[i]+=mod)%=mod,(Y[i]+=mod)%=mod;
	for(i=0;i<lim;i++){
		ta[i]=comp(X[i]&32767,X[i]>>15);
		tb[i]=comp(Y[i]&32767,Y[i]>>15);
	}
	fft(ta,lim);fft(tb,lim);
	for(i=0;i<lim;i++){
		j=(lim-i)%lim;//0的特判
		comp da,db,dc,dd;
  		da=(ta[i]+conj(ta[j]))*comp(0.5,0);
		db=(ta[i]-conj(ta[j]))*comp(0,-0.5);
		dc=(tb[i]+conj(tb[j]))*comp(0.5,0);
		dd=(tb[i]-conj(tb[j]))*comp(0,-0.5);
		a[j]=da*dc+da*dd*comp(0,1);
		b[j]=db*dc+db*dd*comp(0,1);
	}
	fft(a,lim);fft(b,lim);
	for(i=0;i<lim;i++){
		ll da,db,dc,dd;
		da=(ll)(a[i].x/lim+0.5)%mod;
		db=(ll)(a[i].y/lim+0.5)%mod;
		dc=(ll)(b[i].x/lim+0.5)%mod;
		dd=(ll)(b[i].y/lim+0.5)%mod;
		z[i]=(da+((db+dc)<<15)+(dd<<30))%mod;
	}
}
ll qpow(ll x,int p){
	ll ans=1;
	for(;p;p>>=1,x=x*x%mod)if(p&1)ans=ans*x%mod;
	return ans;
}
void polyinv(int A[],int C[],int n){
	int bas,cur,i;
	memset(B,0,sizeof(B));
	B[0][0]=qpow(A[0],mod-2);
	for(mx=2,bas=1,lim=4,cur=1;bas<n*2;mx++,lim<<=1,cur^=1,bas<<=1){
		fft_prepare();
		memset(B[cur],0,sizeof(B[cur]));
		for(i=0;i<=bas;i++)B[cur][i]=(ll)B[cur^1][i]*2%mod;
		conv(B[cur^1],B[cur^1],B[cur^1]);
		conv(B[cur^1],A,B[cur^1]);
		for(i=0;i<=bas;i++)B[cur][i]=((ll)B[cur][i]-B[cur^1][i]+mod)%mod;
	}
	for(i=0;i<n;i++)C[i]=((ll)B[cur^1][i]+mod)%mod;
}
int main(){
	int i,n;
	scanf("%d",&n);
	mod=1000000007;
	for(i=0;i<n;i++)scanf("%d",&A[i]),A[i]%=mod;
	polyinv(A,C,n);
	for(i=0;i<n;i++)printf("%d ",C[i]);
	return 0;
}
```

5. 线性递推

$a_n=\sum_{i=1}^{k}f_i\times a_{n-i}$

输入：第一行$n,k$，第二行$f_1...f_k$，第三行$a_0...a_{k-1}$

求$a_n$

```cpp
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define M 131073
struct comp{
	double x,y;
	comp():x(0),y(0){}
	comp(const double &_x,const double &_y):x(_x),y(_y){}
};
comp operator+(const comp &a,const comp &b){return comp(a.x+b.x,a.y+b.y);}
comp operator-(const comp &a,const comp &b){return comp(a.x-b.x,a.y-b.y);}
comp operator*(const comp &a,const comp &b){return comp(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
comp conj(const comp &a){return comp(a.x,-a.y);}
double PI=acos(-1);
comp w[M+5];
int rev[M+5];
int lim,mx,mod;
void fft(comp *a,int n){
	int i,j,k,lyc;
	for(i=0;i<n;i++)if(i<rev[i]) swap(a[i],a[rev[i]]);
	for(i=2,lyc=n>>1;i<=n;i<<=1,lyc>>=1)
		for(j=0;j<n;j+=i){
			comp *l=a+j,*r=a+j+(i>>1),*p=w;
			for(k=0;k<(i>>1);k++){
				comp tmp=*r**p;
				*r=*l-tmp,*l=*l+tmp;
				++l,++r,p+=lyc;
			}
		}
}
void fft_prepare(){
	int i;
	for(i=0;i<lim;i++)rev[i]=rev[i>>1]>>1|((i&1)<<(mx-1));
	for(i=0;i<lim;i++)w[i]=comp(cos(2*PI*i/lim),sin(2*PI*i/lim));
}
void conv(int *x,int *y,int *z,int n,int m,int mod){
	int i,j;
	static comp ta[M+5],tb[M+5],a[M+5],b[M+5];
	static int X[M+5],Y[M+5];
	for(i=0;i<n;i++)X[i]=(x[i]+mod)%mod;
	for(;i<lim;i++)X[i]=0;
	for(i=0;i<m;i++)Y[i]=(y[i]+mod)%mod;
	for(;i<lim;i++)Y[i]=0;
	for(i=0;i<lim;i++){
		ta[i]=comp(X[i]&32767,X[i]>>15);
		tb[i]=comp(Y[i]&32767,Y[i]>>15);
	}
	fft(ta,lim);fft(tb,lim);
	for(i=0;i<lim;i++){
		j=(lim-i)%lim;//0的特判
		comp da,db,dc,dd;
  		da=(ta[i]+conj(ta[j]))*comp(0.5,0);
		db=(ta[i]-conj(ta[j]))*comp(0,-0.5);
		dc=(tb[i]+conj(tb[j]))*comp(0.5,0);
		dd=(tb[i]-conj(tb[j]))*comp(0,-0.5);
		a[j]=da*dc+da*dd*comp(0,1);
		b[j]=db*dc+db*dd*comp(0,1);
	}
	fft(a,lim);fft(b,lim);
	for(i=0;i<lim;i++){
		ll da,db,dc,dd;
		da=(ll)(a[i].x/lim+0.5)%mod;
		db=(ll)(a[i].y/lim+0.5)%mod;
		dc=(ll)(b[i].x/lim+0.5)%mod;
		dd=(ll)(b[i].y/lim+0.5)%mod;
		z[i]=(((da+((db+dc)<<15)+(dd<<30))%mod)+mod)%mod;
	}
}
int qpow(ll x,int p){
	int ans=1;
	for(;p;p>>=1,x=x*x%mod)if(p&1)ans=ans*x%mod;
	return ans;
}
void polyinv(int *A,int *ans,int n){
	static int B[2][M+5];
	int bas,cur,i;
	memset(B,0,sizeof(B));
	B[0][0]=qpow(A[0],mod-2);
	for(mx=2,bas=1,lim=4,cur=1;bas<n*2;mx++,lim<<=1,cur^=1,bas<<=1){
		fft_prepare();
		memset(B[cur],0,sizeof(B[cur]));
		for(i=0;i<bas;i++)B[cur][i]=(ll)B[cur^1][i]*2%mod;
		conv(B[cur^1],B[cur^1],B[cur^1],bas,bas,mod);
		conv(B[cur^1],A,B[cur^1],bas,bas,mod);
		for(i=0;i<bas;i++)B[cur][i]=((ll)B[cur][i]-B[cur^1][i]+mod)%mod;
	}
	for(i=0;i<n;i++)ans[i]=(B[cur^1][i]+mod)%mod;
}
int flag;
void polydiv(int *a,int *b,int *H,int *R,int n,int m){
	int i;
	static int rF[M+5],rG[M+5],t1[M+5];
	memset(t1,0,sizeof(int)*n);
	for(i=0;i<=n;i++)rF[n-i]=a[i];
	if(!flag){
		flag=1;
		for(i=0;i<=m;i++)rG[m-i]=b[i];
		polyinv(rG,rG,n-m+1);
	}
	mx=0,lim=1;while(lim<n<<1)lim<<=1,mx++;
	fft_prepare();
	conv(rF,rG,H,n,n,mod);
	reverse(H,H+n-m+1);
	for(i=n-m+1;i<=n;i++)H[i]=0;
	mx=0,lim=1;while(lim<n+m)lim<<=1,mx++;
	fft_prepare();
	conv(b,H,t1,m,n,mod);
	for(i=0;i<m;i++)R[i]=((ll)a[i]-t1[i]+mod)%mod;
	for(;i<=n;i++)R[i]=0;
}
int a[M+5],g[M+5];
int D[M+5];
int res[M+5],bas[M+5],n,k;
void mtmul(int *a,int *b){
	mx=0,lim=1;
	while(lim<k*2)lim<<=1,mx++;
	fft_prepare();
	conv(a,b,a,k,k,mod);
	polydiv(a,g,D,a,k*2-2,k);
}
void qpow(int x){
	res[0]=bas[1]=1;
	for(;x;x>>=1,mtmul(bas,bas))
		if(x&1)mtmul(res,bas);
}
int main(){
	int i;
	ll x;
	mod=998244353;
	scanf("%d%d",&n,&k);
	g[k]=mod-1;
	for(i=1;i<=k;i++)scanf("%lld",&x),g[k-i]=(int)(x%mod+mod)%mod;
	for(i=0;i<k;i++)scanf("%lld",&x),a[i]=(int)(x%mod+mod)%mod;
	qpow(n);
	ll ans=0;
	for(i=0;i<k;i++)(ans+=(ll)a[i]*res[i])%=mod;
	printf("%lld\n",ans);
	return 0;
}
```

6. 分治$FFT$

给定$g_1...g_{n-1}$，$f_0=1$，求$f_0...f_{n-1}$

$f_i=\sum_{j=1}^{i}f_{i-j}g_j$

分治计算贡献。

```CPP
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
#define M 524290
struct comp{
	double x,y;
	comp():x(0),y(0){}
	comp(const double &_x,const double &_y):x(_x),y(_y){}
};
comp operator+(const comp &a,const comp &b){return comp(a.x+b.x,a.y+b.y);}
comp operator-(const comp &a,const comp &b){return comp(a.x-b.x,a.y-b.y);}
comp operator*(const comp &a,const comp &b){return comp(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
comp conj(const comp &a){return comp(a.x,-a.y);}
double PI=acos(-1);
comp w[M+5];
int rev[M+5];
int lim,mx,mod;
void fft(comp *a,int n){
	int i,j,k,lyc;
	for(i=0;i<n;i++)if(i<rev[i])swap(a[i],a[rev[i]]);
	for(i=2,lyc=n>>1;i<=n;i<<=1,lyc>>=1)
		for(j=0;j<n;j+=i){
			comp *l=a+j,*r=a+j+(i>>1),*p=w;
			for(k=0;k<(i>>1);k++){
				comp tmp=*r**p;
				*r=*l-tmp,*l=*l+tmp;
				++l,++r,p+=lyc;
			}
		}
}
void fft_prepare(){
	int i;
	for(i=0;i<lim;i++)rev[i]=rev[i>>1]>>1|((i&1)<<(mx-1));
	for(i=0;i<lim;i++)w[i]=comp(cos(2*PI*i/lim),sin(2*PI*i/lim));
}
void conv(int *x,int *y,int *z,int n,int m,int mod){
	int i,j;
	mx=0;lim=1;while(lim<n+m+1)lim<<=1,mx++;
	fft_prepare();
	static comp ta[M+5],tb[M+5],a[M+5],b[M+5];
	static int X[M+5],Y[M+5];
	for(i=0;i<n;i++)X[i]=(x[i]+mod)%mod;
	for(;i<lim;i++)X[i]=0;
	for(i=0;i<m;i++)Y[i]=(y[i]+mod)%mod;
	for(;i<lim;i++)Y[i]=0;
	for(i=0;i<lim;i++){
		ta[i]=comp(X[i]&32767,X[i]>>15);
		tb[i]=comp(Y[i]&32767,Y[i]>>15);
	}
	fft(ta,lim);fft(tb,lim);
	for(i=0;i<lim;i++){
		j=(lim-i)%lim;
		comp da,db,dc,dd;
  		da=(ta[i]+conj(ta[j]))*comp(0.5,0);
		db=(ta[i]-conj(ta[j]))*comp(0,-0.5);
		dc=(tb[i]+conj(tb[j]))*comp(0.5,0);
		dd=(tb[i]-conj(tb[j]))*comp(0,-0.5);
		a[j]=da*dc+da*dd*comp(0,1);
		b[j]=db*dc+db*dd*comp(0,1);
	}
	fft(a,lim);fft(b,lim);
	for(i=0;i<lim;i++){
		ll da,db,dc,dd;
		da=(ll)(a[i].x/lim+0.5)%mod;
		db=(ll)(a[i].y/lim+0.5)%mod;
		dc=(ll)(b[i].x/lim+0.5)%mod;
		dd=(ll)(b[i].y/lim+0.5)%mod;
		z[i]=(((da+((db+dc)<<15)+(dd<<30))%mod)+mod)%mod;
	}
}
int f[M+5]={1},g[M+5],a[M+5];
void solve(int l,int r){
	if(l==r)return;
	int i,mid=(l+r)>>1;
	solve(l,mid);
	for(i=l;i<=mid;i++)a[i-l]=f[i];
	conv(a,g,a,mid-l+1,r-l+1,mod);
	for(i=mid+1;i<=r;i++)f[i]=(f[i]+a[i-l])%mod;
	solve(mid+1,r);
}
int main(){
	int i,n;
	mod=998244353;
	scanf("%d",&n);
	for(i=1;i<n;i++)scanf("%d",&g[i]);
	solve(0,n);
	for(i=0;i<n;i++)printf("%d ",f[i]);
	return 0;
}
```

7. $FMT$与子集卷积

我们比较了解的是有关多项式的乘法运算，对于下标为整数，下标运算为相加等于某个数的时候，我们有很优秀的FFT做法。

但是遇到一些奇怪的卷积形式时，比如我们定义 $h=f∗g$， $h_S=∑_{L⊆S}∑_{R⊆S}[L∪R=S]f_L∗g_R$。

此时下标是一个集合，运算为集合并的卷积，我们已知了 $f$ 和 $g$ ，需要快速算出 $h$。

最暴力的做法是$ O(2^n)$ 分别枚举 $L$ 和 $R$，把答案加到 $h $中去，这样复杂度是 $O(4^n)$，不太行。

这时我们就要一种高效的算法。求卷积可以用分治乘法，好像比较高妙，但我们要讲的是另一种：快速莫比乌斯变换和反演。

类比$FFT$，$FMT$也需要先把 $f$ 和 $g$ 求点值，点值相乘后再插值回去，快速莫比乌斯变换就相当于点值，快速莫比乌斯反演就相当于插值。

具体证明：

我们定义 $f $的莫比乌斯变换为 $f$^ ，其中 $f_S$^$=∑_{T⊆S}f_T$。
相反的，我们定义 $f$^ 的莫比乌斯反演为 $f$，其中 $f_S=∑_{T⊆S}(−1)^{|S|−|T|}f_T$^，用容斥原理易得。
然后我们对卷积式两边同时做莫比乌斯变换：$h_S$^$=∑_{L⊆S}∑_{R⊆S}[L∪R⊆S]f_L∗g_R$。
由于$ [L∪R⊆S]⇔[L⊆S][R⊆S]$，所以 $h_S$^$=∑_{L⊆S}∑_{R⊆S}f_L∗g_R$。
即 $h_S$^$=(∑_{L⊆S}f_L)∗(∑_{R⊆S}g_R)=f_S$ ^$∗g_S$ ^。
于是问题就在于如何快速求出 $f$ 和 $g$ 莫比乌斯变换（反演）。

如果要暴力的话，可以直接枚举子集算出莫比乌斯变换（反演），这样是 $O(3^n)$，虽然比较优秀了，但复杂度还能更低。

我们考虑用递推解决：

设 $f_S$^$(i)$ 表示 $∑_{T⊆S}[(S−T)⊆{1,2,...,i}]f_T$
易得初始状态：$f_S$^$(0)=f_S$
对于每一个不包含 {$i$} 的集合 $S$，可知 $f_S$ ^$(i)=f_S$ ^$(i−1)$（因为 $S$ 并没有$ i$ 这位），$f_{S∪{i}}$^ $(i)=f_S$^ $(i−1)+f_{S∪{i}}$^$(i−1)$（前者的 $T $没有包含 {$i$}，而后者的 T 必须包含了 {$i$}）。
显然，递推了 $n $轮之后，$f_S$^$n$ 就是所求的变换了。
 这样我们就能在 $O(n∗2^n)$ 快速求出 $f$ 的莫比乌斯变换了。（逆莫比乌斯变换同理）

于是我们就解决了集合并卷积的问题。

$UPD$：

我们都知道第一层循环枚举集合，第二层循环枚举它为$1$的位，把去掉这个$1$的子集的答案加上去的做法是错的。我们考虑两个集合$s,t$，其中$t∈s$。t可能有多种路径到达$s$，也就是存在多个$k,k∈s,t∈k$，这样$t$就会被算多次。

这里有一个感性理解的方法，为什么第一层枚举位第二层枚举集合是对的，也就是每一个集合它的所有子集的贡献只被算了一次。

我们假设$k_1$为$t$并上第一个和$s$不一样的位，我们发现$t$的答案会先算到$k_1$上，而对于其他的$k$，在t的答案算上来的时候自己的答案已经会先算上去了。

而对于逆莫比乌斯变换，如果理解了莫比乌斯变换后，其本质就是一个容斥。

 ```cpp
void Fmt(int *a) {
  for (int i = 0; i < n; ++i)
    for (int s = 0; s < U; ++s)
      if (s >> i & 1) a[s] = Add(a[s], a[s ^ (1 << i)]);
}
void Ifmt(int *a) {
  for (int i = 0; i < n; ++i)
    for (int s = 0; s < U; ++s)
      if (s >> i & 1) a[s] = Sub(a[s], a[s ^ (1 << i)]);
}
 ```


（此处$n$为集合大小， $U=2n$）

接下来我们来继续讲一讲子集卷积：

问题是已知 $f$ 和 $g$，我们想求出 $h=f∗g$，其中$ h_S=∑_{T⊆S}f_T∗g_{S−T}$。

 回顾刚刚的集合并卷积，子集卷积的条件比集合并卷积更苛刻，即 $L$ 和 $R$ 的集合应该不相交。

考虑集合并卷积合法当且仅当 $L∩R=∅$，我们可以在卷积时多加一维，维护集合的大小，如 $f_{i,S}$ 表示集合中有 $i $个元素，集合表示为$ S$。可以发现当 $i$ 和 $S $的真实元素个数符合时才是对的。

初始时，我们只把 $f_{bc[S],S}$ 的值赋成原来的$ f_S$（$g $同理），然后对每一个$f_i$做一遍$FMT$，点值相乘时这么写：$h_{i,S}=∑_{j=0}^i f_{j,S}∗g_{i−j,S}$。最后扫一遍把不符合实际情况的状态赋成 0即可。（$bc$[]表示集合元素个数，即$bitcount$）

```cpp
for i = 0 to n
  Fmt(f[i])
  Fmt(g[i])
  for s = 0 to U - 1
    for j = 0 to i
      h[i][s] += f[j][s] * g[i - j][s]
  Ifmt(h[i])
for s = 0 to U - 1
  h[bc[s]][s] is the real answer
```

```CPP
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;
void fmt(int a[],int n,int f){
	int i,j;
	for(i=0;i<n;i++)for(j=0;j<1<<n;j++)if(s&(1<<i))a[j]=a[j]+f*a[j^(1<<i)];
}
int f[N][1<<N],g[N][1<<N],cnt[1<<N];
void subsetconvolution(int n,int a[],int b[],int c[]){
	int i,j;
	for(i=0;i<1<<n;i++)cnt[i]=cnt[i>>1]+(i&1);
	for(i=0;i<1<<n;i++)f[cnt[i]][i]=a[i],g[cnt[i]][i]=b[i];
	for(i=0;i<n;i++){
		fmt(f[i],n,1);
		fmt(g[i],n,1);
		for(j=0;j<1<<n;i++)
			for(k=0;k<i;k++)
				h[i][j]=h[k][j]+f[k][j]*g[i-k][j];
		fmt(h[i],n,-1);
	}
	for(i=0;i<1<<n;i++)c[i]=h[cnt[i]][i];
}
int main(){
	int i;
	return 0;
}
```

8. $FWT$

背板子，没啥可说的。$f_i=\sum_{k\oplus j==i}a_jb_k$

$a_0...a_{2^n-1},b_0...b_{2^n-1}$

```CPP
#include<bits/stdc++.h>
typedef long long ll;
#define N 1<<18
ll a[N],b[N],w=998244353,c[N];
ll div(int x,ll p){
	if(x==1)return p;
	if(p&1)return (p+w)/2%w;
	else return p/2;
}
void fwtor(int f,int l,ll a[]){
	int i,j,k;
	for(i=1;i*2<=l;i<<=1)
		for(j=0;j<l;j+=i*2)
			for(k=0;k<i;k++)
				(a[j+k+i]+=a[j+k]*f)%=w;
}
void fwtand(int f,int l,ll a[]){
	int i,j,k;
	for(i=1;i*2<=l;i<<=1)
		for(j=0;j<l;j+=i*2)
			for(k=0;k<i;k++)
				(a[j+k]+=a[j+k+i]*f)%=w;
}
void fwtxor(int f,int l,ll a[]){
	int i,j,k;ll t;
	if(f==-1)f=2;
	for(i=1;i*2<=l;i<<=1)
		for(j=0;j<l;j+=i*2)
			for(k=0;k<i;k++)
				t=a[j+k],
				(a[j+k]=div(f,t+a[j+k+i]))%=w,
				(a[j+k+i]=div(f,t-a[j+k+i]))%=w;
}
int main(){
	int i,n;
	scanf("%d",&n);
	n=1<<n;
	for(i=0;i<n;i++)scanf("%lld",&a[i]);
	for(i=0;i<n;i++)scanf("%lld",&b[i]);
	fwtor(1,n,a),fwtor(1,n,b);
	for(i=0;i<n;i++)c[i]=a[i]*b[i];
	fwtor(-1,n,a),fwtor(-1,n,b),fwtor(-1,n,c);
	for(i=0;i<n;i++)printf("%lld ",(c[i]%w+w)%w);
	puts("");
	fwtand(1,n,a),fwtand(1,n,b);
	for(i=0;i<n;i++)c[i]=a[i]*b[i];
	fwtand(-1,n,a),fwtand(-1,n,b),fwtand(-1,n,c);
	for(i=0;i<n;i++)printf("%lld ",(c[i]%w+w)%w);
	puts("");
	fwtxor(1,n,a),fwtxor(1,n,b);
	for(i=0;i<n;i++)c[i]=a[i]*b[i];
	fwtxor(-1,n,a),fwtxor(-1,n,b),fwtxor(-1,n,c);
	for(i=0;i<n;i++)printf("%lld ",(c[i]%w+w)%w);
	puts("");
	return 0;
}
```

