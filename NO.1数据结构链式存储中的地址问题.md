# 排坑总结
&nbsp;&nbsp;记录一些错误（可能会很沙雕）
## NO.1（2019.9.20晚一点半）
&nbsp;&nbsp;写数据结构时的地址问题，当时是调试一个程序，测试写的创建队列的函数Create_Queue()，队列里存储的是树的结点，在main函数里做测试数据。


***下面用C语言大概描述一下***
~~~c
    Tree t = (struct TNode*)malloce(sizeof(struct TNode));
    while(1)
    {
        char symbol;
        int weight;
        scanf("%c %d",&symbol,&weight);
        if(weight == 0)
            break;
        else
        {
            t.data = symbol;
            t.priority = weight;
            Enqueue(q,t,weight);
        }
    }
    printf("%c ",q.front->data);
    printf("%c ",q.front->next->data);
    printf("%c ",q.front->next->next->data);
~~~
&nbsp;&nbsp;当时的问题是：不管我输入的是啥，到最后输出的三个都是我最后输入的数据，三个一模一样（当然这是最后调试调试着才这样的），刚开始规律都没找到。
&nbsp;&nbsp;然后发现了这个后，看着看着突然看到了那句
~~~c
Tree t = (struct TNode*)malloce(sizeof(struct TNode));
~~~
&nbsp;&nbsp;然后后面的故事你就应该懂了，只开辟了一块内存，然后后面每次的输入都是将前一次的内容覆盖了，所以每次输出的都是最后一次输入的数据。
&nbsp;&nbsp;通过这次问题加深了对链式存储结构的理解：所谓链式存储结构就是开辟内存中一块块不连续的内存，然后通过额外的指针域将其逻辑上联系起来，像是我犯的错误，我当时想的是把树的节点只要装进这个队列，外面的操作就改变不了它了，然而却不是这样，这个t一直指向的是这块内存，所以当你重新输入的时候就会覆盖这块内存上原有的值，所以哪怕已经存起来了还是会被改变，因为这只是逻辑上的存起来了，事实上还是指针指向的游戏。

### 一个小实验：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019092218453945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3oyMjAxOTg2MTEz,size_16,color_FFFFFF,t_70)
**&nbsp;&nbsp;&nbsp;如果我们开辟一块新的内存空间，然后用p指向它，给其赋值为0，然后用定义另外一个int型的指针q，让它指向和p同一块内存，我们改变q的值，发现p的值也被修改了。这就是因为它两其实都指向同一块内存，而修改其中一个的值，那块内存上的值也就会被修改，所以另一个也就改变了。**


**&nbsp;&nbsp;所以，想想操作链表时的经常会有的一个操作:**
~~~c
bool Insert(LinkList L, element e)
{
    LinkList p = L;
    {
        ......
    }
}
~~~
**是不是细思极恐，其实这也是当时我们班数据结构群里有人问的这么一个问题，突然想到了，拉出来记录一下。**

## 总结：
&nbsp;&nbsp;1.链式存储结构实际上是将一些不连续的内存通过额外的指针域，逻辑上联系在了一起。


&nbsp;&nbsp;2.指针就是对内存的操作，所以如果两个指针指向同一块内存，小心使用，因为改变一个，另一个也会改变！
