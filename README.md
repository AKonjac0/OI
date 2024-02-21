# OI
# 线性代数
## 线性基

### 定义
定义一个p数组，满足对于任意a数组中的数，都能由p中一些元素异或得来，并且保证p元素个数最少。

其中a数组中数的个数为n，最大值约为$2^N$，则p数组的大小为$\Theta(N)$，即将a中的数看成2进制数，p只由N个第i位是最高位1的数组成，1<=i<=N。

至于为什么不使用最高位为1，其他位全为0的数作为基，因为如果贪心地，只要$a$数组第$i$位有1，就加入$2^{i-1}$作为基，但是可能有一个基第$i$位是1，可以表示这个数，那么就不需要$2^{i-1}$，即求出的基不一定是最少的。

### 构造方式
```cpp
void add(int x){
	for(int i=N-1;i>=0;--i){
		if(x&(1ll<<i)){//可以由1<<i消去最高位的1，继续匹配低位的1,注意1ll
			if(!p[i]){//没有对应的p[i]，就创造一个
				p[i]=x;cnt++;break;//cnt表示p中元素个数
			}
			x^=p[i];//有对应的p[i]就消去
		}
	}
	return;
}
```
* 解释：因为a[i]可以由p逐渐消去最高位1，最终得到0，所以**最多**只需要N个"最高位1"数

>3812 

真.模板

* 为什么由线性基从高位到低位贪心异或出来的就是最大值？

因为即便1011111^111111后会使后五位变为零，但是第六位会变1，所以贪心是正确滴。1011111^111111=1100000

* 会不会最大值不能由线性基构造出来？

不可能，因为x1^x1=0,0^x1=x1,就是说，同一个数x1异或任意次只能为x1或0。

如果存在最大值，那么它是a1^a2^...^an得来,就是(x1^x2)^(x1^x3)^...^(x2^x3),而最后一定能化为x1^x2^...^xn.

* 会不会线性基异或出的最大值不能由原数组得出？

不可能，因为每个线性基至少由一个原数组中的数得出。

1.假设一个原数a包含最高位线性基x1和当前线性基x3，一个原数b包含次高位线性基x2和当前线性基x3，那么：

若先a，则不可能有x3出现

若先b，则不可能有x3出现

所以不会出现x3抵消x3的情况，因为x3不可能存在。

2.假设一个数a包含高位x1和低位x2,一个数b包含低位x2,显然a就是最大值.

2.假设一个数a包含高位x1,一个数b包含低位x2,显然a^b就是最大值

所以不可能会有最大值不能由原数组得出的情况。

>P3857 TJOI

模板题，对于每个开关都是一个2进制数，令p为组成a所有数的线性基，因为没说一个数只用一次，那么每个p[i]由被选和不被选两种可能，答案为$2^N$
```cpp
#include<iostream>
#include<cstring>
#include<cstdio>
#define int long long 
using namespace std;
const int N=55;
int a[N<<2],p[N<<2];
int n,m,cnt;
int read1(){
	int x=0;
	char ch=getchar();
	while(ch!='O' && ch!='X'){
		ch=getchar();
	}
	while(ch=='O' || ch=='X'){
		int f=0;
		if(ch=='O')f=1;
		x=(x<<1)+f;
		ch=getchar();
	}
	return x;
}
void add(int x){
	for(int i=N-1;i>=0;--i){
		if(x&(1ll<<i)){
			if(!p[i]){
				p[i]=x;cnt++;break;
			}
			x^=p[i];
		}
	}
	return;
}
int kp(int x,int p){
	if(p<=1)return x;
	if(p%2==0)return kp(x*x,p>>1);
	else return x*kp(x*x,p>>1);
}
signed main(){
	scanf("%lld%lld",&n,&m);
	for(int i=1;i<=m;++i){
		a[i]=read1();
	}
	cnt=0;
	for(int i=1;i<=m;++i){
		add(a[i]);
	}
	printf("%lld",(1ll<<cnt)%2008);
	return 0;
}
```
>P4570 BJWC

~~我是傻逼~~

这道题有一个重要条件，~~也是我没看见的~~，就是
>一个矿石组合会产生“魔法抵消”当且仅当存在一个非空子集，那些矿石的元素序号按位异或起来为零

意思是说，任何魔法石不能异或两遍及以上，每个线性基只能用一次，不管它在每个数中出现多少次，那么每个线性基的贡献就是它第一次在魔法石中出现时的值。要使值最大，就把魔法石按贡献从大到小排序即可。

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
#define int long long 
using namespace std;
const int N=2100,K=63;
int n,x,y,ans;
int p[N];
struct node{
	int x,y;
}q[N];
bool cmp(node a,node b){
	return a.y>b.y;
}
void add(int i){
	int x=q[i].x,y=q[i].y;
	for(int i=K-1;i>=0;--i){
		if(x&(1ll<<i)){//1ll 注意
			if(!p[i]){
				p[i]=x;
				ans+=y;
				break;
			}
			x^=p[i];
		}
	}
	return;
}
signed main(){
	scanf("%lld",&n);
	for(int i=1;i<=n;++i){
		scanf("%lld%lld",&q[i].x,&q[i].y);
	}	
	sort(q+1,q+n+1,cmp);
	for(int i=1;i<=n;++i){
		add(i);
	}
	printf("%lld",ans);
	return 0;
}

```
