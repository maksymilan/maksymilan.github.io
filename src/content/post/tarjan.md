---
title: tarjan
date: 2024-05-25 17:41:39
description: "这篇文章介绍了Tarjan算法的基本原理和实现方法，主要用于求解有向图的强连通分量。"
publishDate: "25 May 2024"
updated: "25 May 2024"
tags: ["tarjan", "algorithm", "graph theory", "strongly connected components"]
---

tarjan算法用来求解一个有向图的强连通分量

算法要求我们

- 有$dfn$数组记录每一个节点在深度优先搜索中的访问次序
- $visited$数组作为全局变量，记录某一个节点是否被访问过
- $Stack$作为栈，储存在一次深度优先搜索中访问到的节点,$top$为栈顶指针，初始值为$-1$,$inStack$数组记录该节点是否在栈中
- $low$数组记录每一个节点能回溯到的最早访问到的节点
- $index$记录访问次序，初始值为0或者1

**算法流程**

对图中每一个节点进行访问，首先判断节点是否被访问过，也就是判断

```c
if(visited[v])
```

如果该节点访问过就跳过该节点，否则就从这个节点开始深度优先搜索

```c
void DFS(int vertex,Gragh G)
```

进入深度优先搜索函数后，我们先进行以下赋值

```c
visited[vertex]=1;
dfn[vertex]=low[vertex]=index++;
Stack[++top]=vertex;
inStack[vertex]=1;
```

然后我们对该节点的所有相邻节点进行深度优先访问

```c
PtrToV p;//p是一个顶点指针
for(p=G->VertexArr[vertex];p!=NULL;p=p->next){
    if(！visited[p->vert]){
        DFS(p->vert,G);
        low[vertex] = min(low[vertex],low[p->vert])
    }//判断p对应的顶点是否访问过
    else if(inStack[p->vert]){
        low[vertex]=min(low[vertex],dfn[p->vert])
    }//如果该节点在栈中，那么说明这个节点到vertex节点之间存在一条路径，并且vertex到这个节点也存在一条路径，也就是说这两个节点之间存在一条回路，我们将其中访问次序靠前的视作树根，更新vertex能回溯到的最早访问的顶点
}
if(dfn[vertex]==low[vertex]){
    while(Stack[top]！=vertex){
        v=Stack[top--];
        print(v);
    }//按照出栈顺序打印顶点
}
```

完整代码如下

```c
void DFS(int vertex,Graph G,void(*visit)(Vertex V));
int min(int a,int b);
int dfn[2*MaxVertices],low[2*MaxVertices],visited[2*MaxVertices],Stack[2*MaxVertices],top=-1,index=0,inStack[2*MaxVertices];
int min(int a,int b){
    return (a<b)?a:b;
}
void StronglyConnectedComponents( Graph G, void (*visit)(Vertex V) ){
    for(int i=0;i<G->NumOfVertices;++i){
        if(visited[i]==0){
            DFS(i,G,visit);
        }
    }
}
void DFS(int vertex,Graph G,void(*visit)(Vertex V)){
    visited[vertex]=inStack[vertex]=1;
    dfn[vertex]=low[vertex]=index++;//记录访问次序
    Stack[++top]=vertex;
    PtrToVNode p;
    for(p = G->Array[vertex];p != NULL;p=p->Next){
        if(!visited[p->Vert]){
            DFS(p->Vert,G,visit);
            low[vertex]=min(low[vertex],low[p->Vert]);
        }
        else if(inStack[p->Vert]){
                low[vertex] = min(low[vertex],dfn[p->Vert]);
            }
    }
    if(dfn[vertex]==low[vertex]){
        while (Stack[top]!=vertex)
        {
            visit(Stack[top]);
            inStack[Stack[top]]=0;
            top--;
        }
        visit(Stack[top]);
        inStack[Stack[top]]=0;
        top--;
        printf("\n");
    }
}
```

