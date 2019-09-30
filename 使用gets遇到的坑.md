# 关于gets中遇到的坑
&emsp;&emsp;那天看小胡的bug时候发现的问题
~~~c
 #include<stdio.h>
int main()
{
	char a[256];
	char b[256];

	//问题：
	scanf("%s",a);			
	gets(b);			//会将scanf后的\n读到
	printf("%s\n",a);
	printf("%s\n",b);
}
~~~
&emsp;&emsp;这样的问题是你scanf完了之后的那个换行符'\n'会被下面的gets读到，然后它读到换行符就直接读取结束了。
&emsp;&emsp;因为scanf读取结束的标志就是'\n'但它又不会读这个换行符，所以这个换行符会被后面的gets读到，它结束的标志也是换行符，就导致了问题。只能输入一次。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019093020332973.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)

## 解决办法
&emsp;&emsp;也很好解决，有两种方法，用一个getchar()来读后面那个'\n'，或者是先gets，再scanf，gets会将换行符也读掉。
~~~c
//解决方法：
	scanf("%s",a);
	getchar();			//把scanf没读到的\n给读了
	gets(b);
	printf("%s\n",a);
	printf("%s\n",b);

	//这样也没问题：
	gets(b);
	scanf("%s",a);
	printf("%s\n",a);
	printf("%s\n",b);
~~~
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190930203733746.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
