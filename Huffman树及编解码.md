# Huffman树——编解码
## 介绍：
> &emsp;&emsp;Huffman树可以根据输入的字符串中某个字符出现的次数来给某个字符设定一个权值，然后可以根据权值的大小给一个给定的字符串编码，或者对一串编码进行解码，可以用于数据压缩或者解压缩，和对字符的编解码。
&emsp;&emsp;**可是Huffman树的优点在哪？**
&emsp;&emsp;1、就在于它对出现次数大的字符（即权值大的字符）的编码比出现少的字符编码短，也就是说出现次数越多，编码越短，保证了对数据的压缩。
&emsp;&emsp;2、保证编的码不会出现互相涵括，也就是不会出现二义性，比如a的编码是00100，b的编码是001，而c的编码是00，，这样的话，对于00100就可能是a，也可能是bc，而Huffman树编码方式不会出现这种问题。

## 如何实现
&emsp;&emsp;实现Huffman树的编解码需要三种数据类型，一个是优先级队列，用来保存树的结点，二是树，用来解码，三是表，用来当作码表编码。下面我们先一一介绍一下三种数据结构：

#### 1、优先级队列
&emsp;&emsp;优先级队列里存放的是一个一个的树的结点，根据树结点中存放的字符的权值来确定其优先级，权重越小，优先级越小，放的位置越靠前。也就是说第一个结点存放的优先级最小，权值最小。
![优先级队列](https://img-blog.csdnimg.cn/20190923203106764.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)

##### 数据类型
~~~c
//优先级队列,struct TNode表示树的结点，在后面介绍
typedef struct QNode
{
	struct TNode* val;			//树的结点,其实也就是数据域
	int priority;				//优先级
	struct QNode* next;			//指针域
}*Node;

typedef struct Queue
{
	int size;			//队列大小
	struct QNode* front;		//队列头指针
}queue;
~~~

#### 2、树
&emsp;&emsp;树里面存放的是字符，以及指向自己的左右孩子结点的指针。比如下图，虽然下图中看起来书中存放了该字符的优先级，但其实可以不加，感觉比较繁琐，所以我取了，但是为了理解方便起见，我在图上标注了出来。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203123932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
##### 数据类型
~~~c
//树
typedef struct TNode
{
	char data;				//字符值
	struct TNode* left;			//左孩子
	struct TNode* right;		        //右孩子
}*Tree;
~~~
#### 3、表
&emsp;&emsp;这个表其实就是一张编码表，里面存放了字符和该字符的编码，用于编码的时候查看。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203138210.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
##### 数据类型
~~~c
//表
typedef struct BNode
{
	char code[256];				//编码
	char symbol;				//字符
	struct BNode* next;			//指向下一个
}*bNode;

typedef struct Table
{
	struct BNode* first;		        //表头
	struct BNode* last;		        //表尾
}*table;
~~~

## 思路
&emsp;&emsp;为了简单起见我们讲述的时候就先将权值设置为用户输入而不是根据出现频率统计，因为我们作业也刚好是用户输入，文章最后我会贴出根据出现频率统计的代码，有兴趣可以看看。因为用到了很多数据类型所以可能写到一半会觉得有点晕，所以我们开始之前先理一下思路：
#### 先设定a，b，c三个数据，它们的权值分别为6，1，2
>&emsp;&emsp;1、首先要根据用户输入的每个字符的权值，创建出一个一个的树结点，然后将其按照优先级的大小存入优先级队列中，按从小到大的顺序，具体实现我会在后面贴。


>&emsp;&emsp;2、根据优先级队列中存放的树的结点构建起一棵树。


>&emsp;&emsp;先出队前两个结点，然后创建一个新的树的结点，新的树的结点的权值就等于出队的两个结点的权值之和，但其没有字符域，也就是说它不是一个真正的树的结点，我们称其为假树结点，对应称为真树结点。
&emsp;&emsp;让出队的两个真树结点作为新得到的假树结点的左右孩子，优先级小的真树结点（也就是先出队的真树结点）作为左孩子，另一个为右孩子。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203151988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
**出队后**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203204266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
**b和c为真树结点，最上面权值为3的为假树结点**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923212345654.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
>&emsp;&emsp;最后将新创建的假树结点又入队，继续循环操作，直到队列只剩一个结点，那个结点就是假树结点，最后也要作为Huffman树的根节点root。

**新的假树结点入队后**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203217146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
**到最后就是下面这样**
队列只剩最后一个假树结点，而且作为所构建Huffman树的根节点root
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019092320325088.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203123932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)

>&emsp;&emsp;3、遍历整棵树建起一张码表，通过观察我们发现，真正有意义的真树结点其实都是叶子节点，所以我们在遍历的时候将所有的叶子节点的编码和字符存入表中即可。
&emsp;&emsp;我们规定遍历树建立表的时候，往左孩子访问一层给码值加0，往右就加1。比如刚刚介绍树的时候贴的那张图，b是00，c是01，a是1。

**下面是建立起来的码表**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203138210.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)

## 构建Huffman树和创建编码表的实现过程
#### &emsp;&emsp;看完思路之后再看实现过程，我们先看创建队列时候的一系列操作：
>&emsp;&emsp;因为为了方便我用了部分C++语法，所以分配内存会是用new，释放内存就是delete，就和C语言里malloc和free是一个作用，其他的都一样。

**&emsp;队列的初始化：**
~~~c
queue Init_queue()
{
	queue q;
	q.size = 0;
	q.front = new struct QNode;
	if (!q.front)
	{
		printf("分配失败！\n");
		exit(1);
	}
	q.front->next = NULL;
	return q;
}
~~~
**&emsp;队列的插入：**
~~~c
//插入，根据优先级
bool EnQueue(queue& q, Tree avl, int weight)
{
	Node newp = new struct QNode;
	newp->val = avl;
	newp->priority = weight;
	if (q.size == 0 || q.front == NULL)         //空表
	{
		newp->next = NULL;
		q.front = newp;
		q.size = 1;
		return true;
	}
	else		//中间位置，需要迭代
	{
		if (weight <= q.front->priority)	//比第一个都小
		{
			newp->next = q.front;
			q.front = newp;
			q.size++;
			return true;
		}
		else    //中间位置
		{
			Node beforp = q.front;
			while (beforp->next != NULL)
			{
				if (weight <= beforp->next->priority)
				{
					newp->next = beforp->next;
					beforp->next = newp;
					q.size++;
					return true;
				}
				else
				{
					beforp = beforp->next;
				}
			}
			//需要插在队列最后
			if (beforp->next == NULL)
			{
				newp->next = NULL;
				beforp->next = newp;
				q.size++;
				return true;
			}
		}
	}
	return true;
}
~~~

**&emsp;创建一个队列：**
需要用户输入每个字符和对应的优先级
~~~c
//创建队列
queue Create_Queue()
{
	queue q = Init_queue();
	while (1)
	{
		char symbol;
		int weight;
		cin >> symbol >> weight;    //C++里的输入，输入symnol和weight

		if (weight == 0)  //如果输入的权值为0，表示输入结束
			break;

		Tree t = new struct TNode;
		t->data = symbol;
		t->left = NULL;
		t->right = NULL;
		EnQueue(q, t, weight);
	}
	return q;
}
~~~

**&emsp;弹出队列中优先级最小的结点：**
~~~c
//弹出队列优先级最小的
Tree Dequeue(queue& q)
{
	if (q.front == NULL)
	{
		cout << "空队！" << endl;
		exit(1);
	}
	Node p = q.front;
	q.front = p->next;
	Tree e = p->val;
	q.size--;
	delete[] p;
	return e;
}
~~~
**&emsp;树的函数，根据优先级队列创建一棵树：**
~~~c
//树的函数
//创建一棵树
Tree Create_Tree(queue& q)
{
	while (q.size != 1)
	{
		int priority = q.front->priority + q.front->next->priority;
		Tree left = Dequeue(q);
		Tree right = Dequeue(q);

		Tree newTNode = new struct TNode;
		newTNode->left = left;
		newTNode->right = right;

		EnQueue(q, newTNode, priority);
	}
	Tree root = new struct TNode;
	root = Dequeue(q);
	return root;
}
~~~
**&emsp;表的函数，根据树创建一张表：**
~~~c
//创建一张表
table Create_Table(Tree root)
{
	table t = new struct Table;
	t->first = NULL;
	t->last = NULL;

	char code[256];
	int k = 0;
	travel(root, t, code, k);
	return t;
}
~~~
**&emsp;表的函数，对travel函数的实现：**
travel函数表示对树的遍历，从而建立起表，采用表尾插入法
~~~c
void travel(Tree root, table& t, char code[256], int k)
{
	if (root->left == NULL && root->right == NULL)
	{
		code[k] = '\0';

		bNode b = new struct BNode;
		b->symbol = root->data;
		strcpy(b->code, code);
		b->next = NULL;

		//尾部插入法
		if (t->first == NULL)		//空表
		{
			t->first = b;
			t->last = b;
		}
		else
		{
			t->last->next = b;
			t->last = b;
		}
	}
	if (root->left != NULL)
	{
		code[k] = '0';
		travel(root->left, t, code, k + 1);
	}
	if (root->right != NULL)
	{
		code[k] = '1';
		travel(root->right, t, code, k + 1);
	}
}
~~~

## 编解码
#### &emsp;&emsp;至此，Huffman树以及编码表已经构建完毕，现在就来实现编解码的函数来检验上述的Huffman树。
**&emsp;编码：**
需要传入编码表来进行编码
~~~c
void EnCode(table t, char* str)
{
	cout << "EnCodeing............./" << endl;
	int len = strlen(str);
	for (int i = 0; i < len; i++)
	{
		bNode p = t->first;
		while (p != NULL)
		{
			if (p->symbol == str[i])
			{
				cout << p->code;
				break;
			}
			p = p->next;
		}
	}
	cout << endl;
}
~~~
**&emsp;解码：**
需要传入Huffman树来进行编码
~~~c
void DeCode(Tree root, char* str)
{
	cout << "DeCode............./" << endl;
	Tree p = root;
	int len = strlen(str);
	for (int i = 0; i < len; i++)
	{
		if (p->left == NULL && p->right == NULL)
		{
			cout << p->data;
			p = root;
		}
		if (str[i] == '0')
			p = p->left;
		if (str[i] == '1')
			p = p->right;
		if (str[i] != '0' && str[i] != '1')
		{
			cout << "The Input String Is Not Encoded correctly !" << endl;
			return;
		}
	}
	if (p->left == NULL && p->right == NULL)
		cout << p->data;
        cout << endl;
}
~~~

## 测试数据
~~~c
int main()
{
	queue q = Create_Queue();
	Tree root = Create_Tree(q);
	table t = Create_Table(root);
	char str[256];
	cout << "请输入要编码的字符：" << endl;
	cin >> str;
	EnCode(t, str);
	cout << "请输入要解码的码值：" << endl;
	char str1[256];
	cin >> str1;
	DeCode(root, str1);
}
~~~

**附上截图：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923220026655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
