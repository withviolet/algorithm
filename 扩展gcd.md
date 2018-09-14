# GCD
gcd就是最大公约数的意思

我们首先要了解扩展gcd，首先要知道gcd，也就是欧几里得算法

即gcd(a,b) = gcd(b, a mod b)   a,b的最大公因数 = b,a mod b的最大公因数

证明如下：

a = kb + r 则 r = a mod b

假设d 为 a b 的最大公约数（d = gcd（a,b））

那么d 也是 r 的最大公约数（因为 r = a - kbd既可以被a除断，也可以被b除断，所以也能被r除断）

即d 是 a mod b的最大公约数（d = gcd（b,a mod b））

从另一头开始证明

假设d 为 b a mod b的最大公约数（d = gcd（b,a mod b））

a = kb + r,d也是a的最大公约数

所以d = gcd（a, b）

得证

并且当b == 0时，a就是a,b的最大公约数（这个我也不造怎么证明，就记下来叭）

有模板如下
```
int gcd(int a, int b){
    return b == 0 ? a : gcd(b, a % b);
}
```
# 扩展GCD
扩展gcd不仅可以求出ab的最大公约数，还可以求出ax + by = gcd(a,b)方程的解

有结论如下x1 = y2, y1 = x2 - (a / b * b)y2  

为啥呢？

证明如下：

我们由欧几里得算法可得gcd(a,b) = gcd(b,a % b)，那么有下面三个等式

ax1 + by1 = gcd(a,b)……（1）

bx2 + (a % b)y2 = gcd(b,a%b)……（2）

（1）= （2）

=> ax1 + by1 = bx2 + (a - (a / b) * b)y2 = ay2 + b(x2 - (a / b)* y2)

所以：

x1 = y2

y1 = x2 - (a / b * b)y2 证毕

并且b == 0时 x = 1 y = 0（just remember this）//d=a，x=1,y=0,此时等式d=ax+by成立

有模板如下
```
ll exgcd(ll a,ll b,ll &x,ll &y){
	if(b == 0){
		x = 1; y = 0;
		return a;
	}
	ll d = exgcd(b, a%b, x, y);
	ll xt = x;
	x = y;
	y = xt - a / b * y;
	return d;
}
```
# 推广
如果我们要求ax + by = n的解呢？

也可以用扩展gcd，求出gcd(a,b)之后，如果n不能被gcd(a,b)整除，就是无解的

如果可以被整除，我们可以得到一组特解x0,y0

由特解可以得到通解的通项公式（为什么成立？直接带到ax + by = gcd(a,b)里试试）

x = x0 + b/gcd(a,b) * t

y = y0 - a / gcd(a,b) * t（t是一个整数）

通常题目需要我们求出方程组最小解

在这里b/gcd(a,b)，a/gcd(a,b)分别是x,y的最小区间，也就是说

x在0 ~ b/gcd(a,b)上只有一个整数解

y在0 ~ a/gcd(a,b)上只有一个整数解

那么这个解就是我们要求的最小整数解



为什么最小区间是b / gcd(a,b)?

证明如下：

设有对特解为x0,y0,x的最小区间为c,y的最小区间为d，则有

x = x0 + ct

y = y0 - dt

带入ax + by = n

ax + by

= a (x0 + ct) + b (y0 - dt)

= ax0 + by0 + act - bdt = n

因为ax0 + by0 = n

所以act - bdt = 0

所以ac = bd，想要用最小的c和d让式子成立，我们就得让ac = bd = ab / gcd(a,b)（其实这个地方思路有点跳，我也不太懂）

可得c = b / gcd(a,b), d = a / gcd(a,b),证毕



所以，最后求得的x = （x0 % （b / gcd（a,b）） + b / gcd(a,b)）%(b / gcd(a,b))

x0%(b / gcd(a,b))让x的范围落在- b / gcd(a,b) ~ + b / gcd(a,b)上 再加上一个 b / gcd(a,b)，区间落在 0 ~ 2(b / gcd(a,b))上

再取模b/gcd(a,b)区间就落在0 ~ b / gcd(a,b)就是我们要求的最小值



下面来看一道题目：POJ2115

644.扩展gcd-时间复杂性 (10分)

C时间限制：3000 毫秒 |  C内存限制：3000 Kb

题目内容：

计算循环语句的执行频次 for(i=A; i!=B ; i+=C) x+=1;
其中A,B,C,i都是k位无符号整数。
输入描述

A B C k, 其中0<k<32
输出描述

输出执行频次数，如果是无穷，则输出“forever”
输入样例

3 7 2 16
输出样例

2 
题目要求(a + xc ) % 2 ^ k 等不等于b（t是任意正整数）

若有解有表达式（a + xc）mod 2^k == b

那么a + xc - b = y * 2^k（y是任意正整数）

cx - 2^k * y = b - a

是不是很像

ax + by = n

我们就把这些数字带进去用exgcd做就可以啦(～￣▽￣)～

代码如下：
```
//POJ2115
#include <iostream>
#include <cstdio>
using namespace std;
typedef long long LL;
LL exgcd(LL a, LL b, LL &x, LL &y){
	if(b == 0){
		x = 1; y = 0;
		return a;
	}
	LL d = exgcd(b, a%b, x, y);
	LL xt = x;
	x = y;
	y = xt - a / b * y;
	return d;
}
int main(){
	LL A,B,C,k;
	while(scanf("%lld%lld%lld%lld",&A,&B,&C,&k)){	
		if(A == 0 && B == 0 && C == 0 && k == 0){
			break;
		}
		LL a = C;
		LL b = (LL)1 << k;
		LL n = B - A;
		LL x,y;
		LL d = exgcd(a,b,x,y);

		if(n % d){
			cout<<"FOREVER"<<endl;
		}else{
			//x = (x0 % (b / gcd(a,b)) + b / gcd(a,b)) % b / gcd(a,b) 
			x = (x * (n / d))%b;

			x = (x % (b / d) + (b / d)) % (b / d);
			cout<<x<<endl;
		}
	}
		
	
	return 0;
}
```
校OJ上有个特殊点0 0 0 0需要输出forever
```
#include <iostream>
#include <cstdio>
using namespace std;
typedef long long LL;
LL exgcd(LL a, LL b, LL &x, LL &y){
	if(b == 0){
		x = 1; y = 0;
		return a;
	}
	LL d = exgcd(b, a%b, x, y);
	LL xt = x;
	x = y;
	y = xt - a / b * y;
	return d;
}
int main(){
	LL A,B,C,k;
	scanf("%lld%lld%lld%lld",&A,&B,&C,&k);	
		if(A == 0 && B == 0 && C == 0 && k == 0){
			cout<<"forever"<<endl;
			return 0;
		}
		LL a = C;
		LL b = (LL)1 << k;
		LL n = B - A;
		LL x,y;
		LL d = exgcd(a,b,x,y);
		if(n % d){
			cout<<"forever"<<endl;
		}else{
			//x = (x0 % (b / gcd(a,b)) + b / gcd(a,b)) % b / gcd(a,b) 
			x = (x * (n / d))%b;

			x = (x % (b / d) + (b / d)) % (b / d);
			cout<<x<<endl;
		}
	return 0;
}
```
