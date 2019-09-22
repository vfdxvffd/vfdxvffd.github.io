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
## 总结：
&nbsp;&nbsp;1.链式存储结构实际上是将一些不连续的内存通过额外的指针域，逻辑上联系在了一起。


&nbsp;&nbsp;2.指针就是对内存的操作，所以如果两个指针指向同一块内存，小心使用，因为改变一个，另一个也会改变！
