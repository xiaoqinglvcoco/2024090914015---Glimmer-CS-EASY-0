# 2024090914015---Glimmer-CS-EASY-02
微光招新题cs2
## 该小节的问题
1. 随机存取是指能够直接访问线性表中的任何一个元素，而不必依次访问前面的所有元素，这是数组的特征（数组中的元素在内存中是连续存储的，通过计算元素的偏移量加上基地址，可以直接定位到任何一个元素。）  
2. - 指针是一个存储另一个变量或数据结构的内存地址的特殊的变量。（我的理解就是存储某个变量的地址）
   - 类型标识符 **指针变量名;
    比如int *p;表示p 是一个指向 int 类型的指针
   - 指针变量的大小是固定的，指针的大小通常为 4 字节（32 位系统）或 8 字节（64 位系统）。无论指针指向什么类型的数据（ int、char 或者 struct ），指针本身的大小都是固定的。
3. - 数组：适用于需要快速随机访问元素的情况，如缓存数据、实现堆栈或队列，像存储班级学生的成绩这样的。  
   - 链表：适用于需要频繁插入或删除元素的情况（应该是由于插入或删除数据的速度比数组快），如实现动态列表。  
   - 栈：适用于需要<mark>后进先出（LIFO）</mark>，如函数调用栈，浏览器历史记录（似乎也是）。  
   - 队列：适用于需要<mark>先进先出（FIFO）</mark>的情况，如任务调度队列，任务队列、打印队列。  （特意加了个高亮doge）
   - 哈希表：适用于需要快速查找、插入和删除的情况，如数据库索引。（顺便搜的）  
   - 树结构（如二叉树、红黑树等）：适用于需要维护有序集合的情况（我认为就是元素之间存在层级关系的场景），如文件系统目录结构。   
   - 图结构：适用于表示对象间的关系，如社交网络、路由算法，交通网络等
4. - 我自己举个例子吧（可能不太恰当，但我只能找到这个例子了，代码是纯手敲的qwq）  
第一行为整数n。  
第2行到第n+1行（共n行） ，每行两个整数x和y，描述了一个点的坐标。  
第n+2行为一个整数m，表示图中连线的个数。  
此后的m 行，每行描述一条连线，由两个整数i和j组成，表示第i个点和第j个点之间有连线。  
5   
0 0  
2 0  
2 2  
0 2  
3 1  
5   
1 2  
1 3  
1 4  
2 5  
3 5  
1 5  
```
    int n,m,x[105],y[105];
    double g[105][105];
    scanf("%d",&n);
    for(int i=1;i<=n;i++)
		scanf("%d%d",&x[i],&y[i]);
	scanf("%d",&m);
	for(int i=1;i<=m;i++)
	{
		int r,c;
		scanf("%d%d",&r,&c);
		g[r][c]=g[c][r]=sqrt((x[c]-x[r])*(x[c]-x[r])+(y[c]-y[r])*(y[c]-y[r]));//两点间距离公式，这里将c，r两个点的距离保存在g[r][c]和g[c][r]上，然后这是无向图的做法。如果是有向图的话只需保存在g[r][c]即可（我都理解是这样的）。
	}
```
## 数据结构的运用
### Part1
结果输出： 1 1 1 1 2 1 2 2 1 1 2 1 2 1 3 2 1 1 1 1 1 1 1 2 2 2 1 2 2 1 1 2 2 
```c
#include <stdio.h>  
#include <stdlib.h>  
#include<string.h>
typedef struct ListNode
{  
    int data;  
    struct ListNode *next;  
}ListNode;
typedef struct List
{
    ListNode *head;
    int size;//刚开始定义了个size后面才发现其实没啥用，只要一个头指针计划好了但是懒得删了（）
}List;
char command[100];
List* iniList()//初始化链表
{
    List *cll = (List*)malloc(sizeof(List));
    if(!cll) return NULL;
    cll->head = (ListNode*)malloc(sizeof(ListNode));
    if (!cll->head)
	{
        free(cll);
        return NULL;
    }
	cll->head->data=1;//初始节点数据为1
	cll->head->next=cll->head;//初始化为环形
	cll->size=1;
	return cll;
}
void Head(List *cll,int data)//在头部插入节点
{  
	ListNode *newNode=(ListNode*)malloc(sizeof(ListNode));
	if(!newNode) return;
	newNode->data=data;
    newNode->next=cll->head;//新节点指向原头节点
	//if(cll->size==1) cll->head=newNode;//如果原本只有一个节点,则更新头节点指向自身为环  
    struct ListNode* temp =cll->head;  
    while(temp->next!=cll->head) temp=temp->next;//遍历到最后一个节点
    temp->next=newNode;
    cll->head=newNode;//原头节点指向新节点
	cll->size++;
}
void Tail(List *cll,int data)//在尾部插入节点
{
    ListNode *newNode = (ListNode*)malloc(sizeof(ListNode)); 
    if(!newNode) return; 
    newNode->data=data;  
    ListNode *temp=cll->head;  
    while(temp->next!=cll->head)//找到最后一个节点
        temp=temp->next;
    temp->next=newNode;//最后一个节点指向新节点
    newNode->next=cll->head;//新节点指向头节点，形成环
    cll->size++;
}
void deleteNode(List *cll,int location)//删除指定位置的节点
{  
    if(location<1||location>cll->size) return;  
    ListNode *temp=cll->head;  
    if(location==1)//删除头节点
	{
        while (temp->next!=cll->head)//找到最后一个节点
            temp=temp->next;
        temp->next=cll->head->next;//最后一个节点指向原头节点的下一个节点
        if(cll->size==1) cll->head=temp;//如果只有一个节点，则头节点指向自己
        free(cll->head);
		cll->head=temp->next;
    }
	else
	{
        for(int i=1;i<location-1;i++)
            temp=temp->next;  
        ListNode *toDelete=temp->next;
        temp->next=toDelete->next;
        free(toDelete);
    }
    cll->size--;
}
void processCommand(List *cll)//读取并解析命令
{
    int data[100],numdata=0;
    if(strcmp(command,"H")==0||strcmp(command,"T")==0)
	{
        while(scanf("%d",&data[numdata]))
		{
            numdata++;
            if(getchar()=='\n') break;//检测到换行符则停止读取
        }
        ungetc(getchar(), stdin);//移除最后一个换行符导致的多余读取,根据命令插入数据
        if(strcmp(command,"H")==0)
        	for(int i=numdata-1;i>=0;i--) Head(cll,data[i]);
		else
        	for(int i=0;i<numdata;i++) Tail(cll,data[i]);
    }
	else if(strcmp(command,"D")==0)
	{
        int location;
        scanf("%d",&location);
        deleteNode(cll,location);
    }
	return;
}
void printList(List *cll)//打印链表
{
//  if (cll->size == 0)
//	{
//      printf("List is empty.\n");
//      return;
//  }
    ListNode *temp=cll->head;
    do
	{
        printf("%d ",temp->data);
        temp=temp->next;
    }
	while(temp!=cll->head);//当回到原头节点时停止
}
void freeList(List *cll)//释放链表内存
{
    if (!cll) return;
    ListNode *temp;
    ListNode *current = cll->head->next;//跳过初始头节点
    while (current != cll->head)
	{
        temp=current;
        current=current->next;
        free(temp);
    }
    free(cll->head);
    free(cll);
}
int main()
{  
    List *cll=iniList();
	while(scanf("%s",command))
	{
		processCommand(cll);
		if(strcmp(command,"C")==0) break;
	}
    printList(cll);
    freeList(cll);
    return 0;  
}
```
### Part2
.out文件的运行截图
[![pAYskt0.png](https://s21.ax1x.com/2024/10/12/pAYskt0.png)](https://imgse.com/i/pAYskt0)
咱就在第一小题的代码上改了下（这里有点不太懂输入数据是不是和第一题一样的）
```c
#include <stdlib.h>  
#include<string.h>
#include<stdio.h>
typedef struct ListNode
{  
    int data;  
    struct ListNode *next;  
}ListNode;
typedef struct List
{
    ListNode *head;
    int size;
}List;
char command[100];
List* iniList()//初始化链表
{
    List *cll = (List*)malloc(sizeof(List));
    if(!cll) return NULL;
    cll->head = (ListNode*)malloc(sizeof(ListNode));
    if (!cll->head)
	{
        free(cll);
        return NULL;
    }
	cll->head->data=1;//初始节点数据为1
	cll->head->next=cll->head;//初始化为环形
	cll->size=1;
	return cll;
}
void Head(List *cll,int data)//在头部插入节点
{  
	ListNode *newNode=(ListNode*)malloc(sizeof(ListNode));
	if(!newNode) return;
	newNode->data=data;
    newNode->next=cll->head;//新节点指向原头节点
	if(cll->size==1) cll->head=newNode;//如果原本只有一个节点,则更新头节点指向自身为环  
    struct ListNode* temp =cll->head;  
    while(temp->next!=cll->head) temp=temp->next;//遍历到最后一个节点
    temp->next=newNode;
    cll->head=newNode;//原头节点指向新节点
	cll->size++;
}
void Tail(List *cll,int data)//在尾部插入节点
{
    ListNode *newNode = (ListNode*)malloc(sizeof(ListNode)); 
    if(!newNode) return; 
    newNode->data=data;  
    ListNode *temp=cll->head;  
    while(temp->next!=cll->head)//找到最后一个节点
        temp=temp->next;
    temp->next=newNode;//最后一个节点指向新节点
    newNode->next=cll->head;//新节点指向头节点，形成环
    cll->size++;
}
void deleteNode(List *cll,int location)//删除指定位置的节点
{  
    if(location<1||location>cll->size) return;  
    ListNode *temp=cll->head;  
    if(location==1)//删除头节点
	{
        while (temp->next!=cll->head)//找到最后一个节点
            temp=temp->next;
        temp->next=cll->head->next;//最后一个节点指向原头节点的下一个节点
        if(cll->size==1) cll->head=temp;//如果只有一个节点，则头节点指向自己
        free(cll->head);
		cll->head=temp->next;
    }
	else
	{
        for(int i=1;i<location-1;i++)
            temp=temp->next;  
        ListNode *toDelete=temp->next;
        temp->next=toDelete->next;
        free(toDelete);
    }
	cll->size--;
}
void circularizeList(List *cll)
{  
    if (!cll || !cll->head) return;
    ListNode *temp = cll->head;  
    while (temp->next != cll->head)
    { // 找到最后一个节点  
        temp = temp->next;  
    }
    temp->next = cll->head; // 确保最后一个节点指向头节点  
}
void processCommand(List *cll)//读取并解析命令
{
    int data[100],numdata=0;
    if(strcmp(command,"H")==0||strcmp(command,"T")==0)
	{
        while(scanf("%d",&data[numdata]))
		{
            numdata++;
            if(getchar()=='\n') break;//检测到换行符则停止读取
        }
        ungetc(getchar(), stdin);//移除最后一个换行符导致的多余读取,根据命令插入数据
        if(strcmp(command,"H")==0)
        	for(int i=numdata-1;i>=0;i--) Head(cll,data[i]);
		else
        	for(int i=0;i<numdata;i++) Tail(cll,data[i]);
    }
	else if(strcmp(command,"D")==0)
	{
        int location;
        scanf("%d",&location);
        deleteNode(cll,location);
    }
	return;
}
int main()
{  
    List *cll=iniList();
	while(scanf("%s",command))
	{
		processCommand(cll);
		if(strcmp(command,"C")==0) break;
	}
    circularizeList(cll);
	ListNode *current=(ListNode*)malloc(sizeof(ListNode));
    current=cll->head;
    ListNode *pre=(ListNode*)malloc(sizeof(ListNode));
	while(current->data!=3)
    {
        pre=current;
        current=current->next;//寻找data=3的节点
    }
	FILE* file=fopen("Josephus.out","w");
    for(int m=1;m<=cll->size;m++)
	{
        for(int i=1;i<m;i++)
        {
            pre=current;
            current=current->next;
        }
		fprintf(file,"%d ",current->data);//输出当前节点的数据到文件
        pre->next=current->next;
        current=pre->next;//从链表中删除当前节点(个人理解为或者说跳过这个节点)
    }
	fclose(file);
	free(cll);
	return 0;
}
```
### Part3
+ 解密过程输出：入栈k 入栈i 入栈g 出栈g 入栈l 出栈l 出栈i 入栈n 入栈m 出栈m 入栈r 入栈m 出栈m 入栈e 出栈e 出栈r 入栈i 出栈i 出栈n 入栈a 入栈h 出栈h 入栈e 出栈e  出栈a 入栈n 入栈r 出栈r 入栈t 出栈t 出栈n 入栈e 入栈o 出栈o 入栈f 出栈f 出栈e 入栈4 入栈a 出栈a 入栈r 出栈r 出栈4 入栈d 出栈d 入栈a 出栈a 入栈r 出栈r 出栈k
+ 最终结果[![pAYsSXQ.png](https://s21.ax1x.com/2024/10/12/pAYsSXQ.png)](https://imgse.com/i/pAYsSXQ)
即glimmerinheartnofear4dark
+ 下面是输出解密结果的代码
```c
#include<stdio.h>
#include <stdlib.h>  
#include<string.h>
int a[50];
typedef struct Node
{
    char str;
    struct Node *next;
}Node;
typedef struct stack
{
    Node *top;
    int size;
}stack;
stack *creatstack()
{
    stack *mystack=(stack*)malloc(sizeof(stack));
    mystack->top=NULL;
    mystack->size=0;
    return mystack;
}
void push(stack *mystack,char c)
{
    Node *newNode=(Node*)malloc(sizeof(Node));
    newNode->str=c;
    newNode->next=mystack->top;
    mystack->top=newNode;
    mystack->size++;
}
void pop(stack *mystack)
{
    if(mystack->size==0) return;
    Node *nextNode=(Node*)malloc(sizeof(Node));
    nextNode=mystack->top->next;
    free(mystack->top);
    mystack->top=nextNode;
    mystack->size--;
}
char gettop(stack *mystack)
{
    if(mystack->size==0) return '\n';
    return mystack->top->str;
}
int empty(stack *mystack)
{
    if(mystack->size==0) return 1;
    else return 0;
}
int main()
{
    char ch[100];
    int len=0;
    for(int i=1;i<=34;i++)
        scanf("%d",&a[i]);//输入在part2里得到的数据3 1 1 2 2 1 2 1 1 2 1 2 2 1 1 2 2 1 1 2 2 1 1 2 2 1 1 2 1 1 1 1 1 2 
    fgets(ch,sizeof(ch),stdin);//读取字符串
    stack *mystack=creatstack();
    for(int i=1;i<=34;i++)
    {
        if(i%2==1)
            for(int j=1;j<=a[i];j++)
            {
                push(mystack,ch[len]);
                //printf("入栈%c ",ch[len]);
                len++;
            }
        else
            for(int j=1;j<=a[i];j++)
            {
                printf("%c",gettop(mystack));
                pop(mystack);
            }
    }
    free(mystack);
    return 0;
}
```