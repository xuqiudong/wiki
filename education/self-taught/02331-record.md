---
title: 02331数据结构随手记
description: 02331数据结构随手记
published: true
date: 2022-10-21T23:15:12.937Z
tags: study
editor: markdown
dateCreated: 2022-10-21T11:53:03.603Z
---

## 数据结构

### 填空

1. 四种基本存储方法：顺序存储、链接存储、**索引存储**、散列存储
2. 递归求解过程的最小问题：**递归出口**
3. n元素，选择排序，最好时间复杂度：**O(n^2)**
4. 快速排序时间复杂度 **o(nlogn)**
5. **递归表**（广义表）的深度是∞
6. **分块查找**。又称为索引顺序查找 =  顺序查找 + 二分法查找
7. 树转二叉树：兄弟加线；去掉非长子节点；旋转； （根只有左子树）
8. 森林转二叉树：每棵树转二叉树；根依次连线；
9. 二叉树转树/森林：x的左孩子和x的父节点连起来；去除和有孩子的节点
10. B树：根至少2个子女；非根关键字[m/2]-1 —— m-1;根最少有1个关键字
11. 在无需数组中应使用 **顺序查找**
12. 稀疏矩阵采用压缩存储，只保存非零要素，得到的顺序存储结构为：**三元组表**
13. 顺序表 - 随机访问- o(1)
14. 顶点表示活动、边表示活动间先后关系的有向无环图称为 **顶点活动 **网, 顶点活动网用拓扑排序产生的序列叫**拓扑序列**
15. 长度M的循环队列，队满：**(read + 1) % M == front**
16. 算法准则：输入、输出、确定性。有穷性、可行性；
17. 二叉树：n2 = n0-1;
18. 基数排序复杂度：问题的规模和分量个数、基数大小
19. 二次探测法增量序列：加减1,2,3...的2次方；
20. 拓扑排序利用栈保存入度为0的顶点目的：减少找入度为0的顶点的时间，提高排序的效率

### 编码

1. **编写函数，在带头结点的单链表L中删除数据在指定范围内(x<=data<=y)的节点**

```c
void f(LinkList head, int x, int y){p
    LinkList q,p; p = head;
    while(p->next != NULL) {
        if(p->next->data >=x && p->next->data <=y) {
            q =p-> next;
            p->next = q->next;
            free(q);
        }else {
            p = p -> next;
        }
    }
}
```

2. **递归方式求二叉树所有阶段data域的值之和**

```c
int f34(BinTree t){
    int left,right = 0;
    if(t == NULL) return 0;
    left= f34(t->lchild);
    righ= f34(t->rchild);   
    return left + right + t->data; 
}
```

3. **将给定的单链表h1,数据按原次序复制到双向链表h2中**

```c
void f(LinkList h1, DLinkList h2){
    LinkNode * temp = h1;
    DLNode * node, *pr = null;
    while(temp != NULL){
        node = (DLNode *) malloc(sizeof(NLNode));
        node->data = temp->date;
        node->next = null;
        node->pre = pre;
        temp = temp->next;
        if(h2 == NULL) {
            h2 = node;
            pre = node;
        }else {
            pre->next = node;
            pre = node;
        }
    }
}
```

4. **求非空二叉树中序遍历的第一个元素和最后一个元素的data域的值**

```C
void f(Tree t, int *first, int *last){
    Tree p;
    p = t;
    while(p->lchild != NULL){
        p = p->lchild;
    }
    *first = p->data;
    p = t;
    while(p -> rchild != NULL){
        p = p->rchild;
    }
    *last = p->data;
}
```

5. **根据输入的字符串，建立不含重复的字符链表**

```c
int f(LinkList h, char string[]){
    LinkList p, q;
    char *pc;
    int count = 0;
    
    pc = string;
    while(*pc !='\0'){
        p = h;
        while(p->next != NULL){
            if (p->next->ch != *pc) {
                p = p->next
            }else {
                break;
            }
        }
        if(p ->next == NULL) {
            q = (LinkList)malloc(sizeof(ListNode));
            q->ch = *pc;
            q->next= NULL;
            p->next = q;
            count++;
        }
        pc++;
    }
    return count;
}
```

6. **把二叉树修改为镜像二叉树**

```c
void f(Tree t){
    if(t == NULL) return;
    Tree left = t->lchild;
    Tree right = t->rchild;
    f(left);
    f(right);
    t->rchild = left;
    t->lchild = right;
         
}
```

7. **计算二叉树中数据域大于等于x的节点的个数，并返回该值**

```c
int f(Tree *t, int x){
    if(t == NULL) return 0;
    int n =  0;
    if(t->data >= x) {
        n = 1;
    }
    return n + f(t->left, x) + f(t->right, x);
}
```

8. **单链表L长度大于1，判断全部的n个数据是否是斐波那契数列**

```c
int iSf(List head){
    ListNode p1= head->next, p2= p1->next, p3= p2->next;
    if(p1->data !=0 || p2->Data != 1) return 0;
    while(p3!= NULL){
        if(p3->data != (p1->data + p2->data) return 0;
        else {
            p1 = p2;
            p2= p3;
            p3 = p3->next;
        }
    }
    retutn 1;
}
```

9. **选择排序**

```c
void f(int A[], int n){
    int i, j,k;
    for(i=0; i<n; i++) {
        k = i;
        for(j=i+1;j<n; j++) {
            if(A[k]>A[j]){
                k = j
            }
        }
        if(k != i) {
            int temp = A[k];
            A[k] = A[i];
            A[i] = temp;
        }
    }
}
```

10. **冒泡排序**

```c
int f(SeqList r, int n){
    for(int i=0;i<n-1;i++){
        for(int j=0; j<n-1-i; j++) {
            if(r[j].key<r[j+1].key) {
                //swap
            }
        }
    }
    return 0;
}
```

11. **后序遍历二叉树**

```c
void p(Tree t) {
    if(t == NULL) return;
    p(t->left);
    p(t->right);
    printf("%c", t->data);
    
    
}
```

12. **带头结点单链表原地逆转**

```C
void f(List head){
    if(head == null || head->next == null) return;
    
    ListNode begin = head->next, 
    end = begin->next;
    while(end != NULL) {
        begin->next  = end->next;
        end->next = head ->next;
        head->next = end;
        end = begin->next;
    }
    
}
```

