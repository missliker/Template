用一种数据结构来维护一些数，需要提供以下操作：

1.插入x

2.删除x（若有多个仅删除1个）

3.查询x的rank（排名定义为比x小的数的个数+1）

4.查询rank为k的数x

5.找x的前驱（前驱定义为小于x且最大的数）

6.找x的后继（后继定义为大于x且最大的数）



二叉查找树（Binary Search Tree, BST)允许快速查找，插入或者删除某一节点，其满足左小右大，中序遍历有序

Splay（伸展树）是一种平衡二叉树，它通过不断将某个节点旋转到根节点，使得整棵树仍然满足BST的性质，并且保持平衡而不至于退化为链

![image-20241026171239590](C:\Users\wink\Documents\Markdown\模板2.0\assets\image-20241026171239590.png)

![image-20241026173922532](C:\Users\wink\Documents\Markdown\模板2.0\assets\image-20241026173922532.png)



![image-20241026174711651](C:\Users\wink\Documents\Markdown\模板2.0\assets\image-20241026174711651.png)