## Prim

```cpp
#include<stdio.h>
#define MAX 200005
#define inf 214700000
struct edge{
	int end;
	int length;
	int nearedge; 
}ed[2*MAX];//无向图，开两倍
int n,m,cnt=1,head[MAX];//记录与i相连最后一条边 
int dis[MAX];//与已建成树的距离 
int ans;
int visited[MAX];
void prim();
int min(int a,int b){return a>b?b:a;} 
void createedge(int,int,int);
int main(){
	int i,start,end,length;
	scanf("%d%d",&n,&m);
	for(i=0;i<m;i++){
		scanf("%d%d%d",&start,&end,&length);
		if(start==end)continue;
		createedge(start,end,length);
		createedge(end,start,length); 
	}
	for(i=2;i<MAX;i++)dis[i]=inf;//初始化dis 
	prim(); 
	return 0;
}
void createedge(int start,int end,int length){
	ed[cnt].end=end;
	ed[cnt].length=length;
	ed[cnt].nearedge=head[start];
	//nearedge：若start只连一条边，则为0；否则为上一条边的位置 
	head[start]=cnt;//过会r遍历从这r开始 
	cnt++;
}
void prim(){//prim算法 
	int i,pointnum=1;//已加入点数 
	int temp=1;//当前扩展点 
	for(i=head[1];i;i=ed[i].nearedge){//找与i相连的边,注意重边 
		dis[ed[i].end]=min(dis[ed[i].end],ed[i].length);
	}
	while(pointnum<n){
		int minlength=inf;
		visited[temp]=1;
		for(i=1;i<=n;i++){//找最短边 
			if(!visited[i]){
				if(dis[i]<minlength){
					minlength=dis[i];
					temp=i;
				}
			}
		}
		for(i=head[temp];i;i=ed[i].nearedge){//更新dis 
			if(!visited[ed[i].end])
				dis[ed[i].end]=min(dis[ed[i].end],ed[i].length);
		}
		ans+=minlength;
		pointnum++;
	}
	printf("%d",ans);
}
```

