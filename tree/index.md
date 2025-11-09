# 树


## ***树***

1. ***引出***
   
   - *当我们在刷算法的时候 当中有个思路就是空间换时间，因为在如今科技发达的社会 硬件已经很牛逼了，所以相比较，时间更为重要*
   - *还有个思路就是 **升维**这个思想，链表其实就是一维，跳表就是二维，将链表升维后 时间复杂度也从 O(n) 优化到了 O(logn)*
   - *树也是一样的 通过链表升维而诞生的一个数据结构*

2. ***树的定义***
   
   - ![树](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242223386.png)

3. ***二维数据结构***
   
   - *常见的二维数据结构 - 树 / 图*
   - *树和图的差别就是在于有没有环，环就是指 树有一个节点指向其他的节点 形成一个环*
   - ***链表就是特殊的树，因为有树在极端的情况下 类似链表***
   - ***树就是特殊的图，没有环的图就是树***

4. ***树的实现代码***
   
   - ```go
     type TreeNode struct {
       val int
       left *TreeNode
       right *TreeNode
     }
     ```
   
   - ```python
     class TreeNode:
         def __init__(self, val):
             self.val = val
             self.left, self.right = None, None
     ```
   
   - ```cpp
     struct TreeNode {
       int val;
       TreeNode *left;
       TreeNode *right;
       TreeNode(int x): val(x), left(NULL), right(NULL)
     }
     ```

## ***二叉树***

1. ***什么是二叉树***
   - *二叉树就是每个节点都只有两个子节点 当然也会有三叉树这样的存在*
   - ![二叉树 - 斐波那契数列](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242231598.png)

## ***二叉搜索树***

1. ***什么是二叉搜索树***
   - ***只需要满足以下特征的就是二叉搜索树***
     - *左子树所有节点都小于子树根节点*
     - *右子树所有节点大于子树根节点*
     - *以此类推，左右子树都分别满足这个性质*
   - ![二叉搜索树](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242233600.png)

## ***树的遍历***

```python
def preorder(self, root):  # 前序遍历
  if root:
    self traverse_path.append(root.val)
    self preorder(root.left)
    self preorder(root.right)

def inorder(self, root):  # 中序遍历 二叉搜索树中序遍历一定升序的
  if root:
    self inorder(root.left)
    self traverse_path.append(root.val)
    self inorder(root.right)

def postorder(self, root):
  if root:
    self postorder(root.left)
    self postorder(root.right)
    self traverse_path.append(root.val)
```

```go
func preorderTraversal(root *TreeNode) (res []int) { // 前序遍历 根-左-右
    var preorder func(node *TreeNode)
    preorder = func(node *TreeNode) {
        if node == nil {
            return
        }
        res = append(res, node.Val)
        preorder(node.Left)
        preorder(node.Right)
    }
    preorder(root)
    return res
}

func inorderTraversal(root *TreeNode) (res []int) { // 中序遍历 左-根-右
    var inorder func(node *TreeNode)
    inorder = func(node *TreeNode) {
        if node == nil {
            return
        }
        inorder(node.Left)
        res = append(res, node.Val)
        inorder(node.Right)        
    }
    inorder(root)
    return res
}

func postorderTraversal(root *TreeNode) (res []int) { // 后序遍历 左-右-根
    var postorder func(node *TreeNode)
    postorder = func(node *TreeNode) {
        if node == nil {
            return
        }
        postorder(node.Left)
        postorder(node.Right)
        res = append(res, node.Val)
    }
    postorder(root)
    return res
}
```

```c
class Solution {  // 前序遍历
public:
    void preorder(TreeNode* root, vector<int> &res) {
        if (root == nullptr) return;
        res.push_back(root->val);
        preorder(root->left, res);
        preorder(root->right, res);
    }

    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        preorder(root, res);
        return res;
    }
};

class Solution { // 中序遍历
public:
    void inorder(TreeNode* root, vector<int> &res) {
        if (root == nullptr) return;
        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }

    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root, res);
        return res;
    }
};


class Solution {  // 后序遍历
public:
    void postorder(TreeNode* root, vector<int>& res) {
        if (root == nullptr) return;
        postorder(root->left, res);
        postorder(root->right, res);
        res.push_back(root->val);
    }
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        postorder(root, res);
        return res;
    }
};
```

## ***树的遍历 - 题目推荐***

```go
/*
    https://leetcode-cn.com/problems/binary-tree-preorder-traversal/submissions/  二叉树的前序遍历
    https://leetcode-cn.com/problems/binary-tree-inorder-traversal/submissions/  二叉树的中序遍历
    https://leetcode-cn.com/problems/binary-tree-postorder-traversal/submissions/ 二叉树的后序遍历
*/

/*
    https://leetcode-cn.com/problems/validate-binary-search-tree/submissions/ 验证二叉搜索树
    https://leetcode-cn.com/problems/invert-binary-tree/submissions/   翻转二叉树
*/
```

## ***平衡二叉搜索树***

1. ***引出***
   - *树的时间复杂度经过升维的思想 从链表的O(n) 变成了 O(logn)，这个“n” 就是树的高度*
   - *但是在极端的树的情况会变成一个链表，时间复杂度会从O(logn) 退化成 O(n)，如下图*
   - ![极不平衡的二叉搜索树 - 等同于链表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242238903.png)
   - ***所以 维护一个树的平衡是很重要的，所以平衡二叉搜索树它lei了 ***
2. ***什么是平衡二叉搜索树***
   - *没有过深的左右子树 相对来说他们是平衡的*
   - *所以为了平衡 在insert delete操作要去动态的维持平衡*
3. ***平衡二叉搜索树的种类***
   - *AVL / 红黑树 / b-tree / b+ tree*

## ***AVL***

1. ***什么是AVL树***
   - *AVL树得名于它的发明者G. M. Adelson-Velsky和Evgenii Landis*
   - *AVL 有一个概念是平衡因子（balance factor），是他的左子树高度减去右子树的高度（相反亦此）*
   - ***balance factor = { -1, 0, 1 }***
   - *AVL又可以叫做 **高度平衡二叉搜索树***
   - *他在insert delete操作时通过旋转来进行平衡*
2. ***AVL的缺点***
   - *他的查询效率很快 但是他每个节点都要存储额外信息，且调整次数频繁*
3. ***AVL示例图***
   - ![AVL](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220727222109863.png)
4. ***AVL的四种旋转***
   - ![avl 四种旋转](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302251234109.jpg)

## ***红黑树***

1. ***引出***
   - *我们已经了解了AVL这个高度平衡二叉搜索树，也已经知道他的缺点，所以红黑树又lei了*
2. ***什么是红黑树***
   - *红黑树是一种近似平衡二叉搜索树*
   - *他的特点是保证任何一个节点的左右子树的高度差小于两倍*
3. ***红黑树的五大特点***
   - *每个节点要么是黑色，要么是红色*
   - *根节点一定是黑色*
   - *每个叶节点（nil节点）是黑色*
   - ***不能有相邻的两个红色节点***
   - ***从任意节点出发到其每个叶节点的所有路径都包含相同数目的黑色节点***
4. ***重点在于 `不能有相邻的两个红色节点` `从任意节点出发到其每个叶节点的所有路径都包含相同数目的黑色节点` 保证了任何一个节点的左右子树的高度差小于两倍***
5. ***红黑树图示***
   - ![红黑树](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242255572.png)
6. ***红黑树与AVL的对比***
   - AVL更适合查询 因为他更平衡 / 红黑树只是近似平衡*
   - *红黑树更适合插入 因为他插入的时候 不需要像avl那样时刻保持平衡 他只需要满足两倍差即可*

## ***字典树***

1. ***什么是字典树***
   
   - *字典树 即trie树，又称前缀树，是一种树形结构*
   - ***典型应用是用于统计和排序大量的字符串(但不仅限于字符串) 所以经常被搜索引擎系统用于文本词频统计***

2. ***字典树的基本性质***
   
   - *节点本身不存完整的单词*
   - *从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串*
   - *每个节点的所有子节点路径代表的字符都不相同*

3. ***字典树图示***
   
   - ***Trie 树的本质，就是利用字符串之间的公共前缀，将重复的前缀合并在一起，比如我们有`["hello","her","hi","how","see","so"]` 这个字符串集合***
   - ![Trie树图示](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242304144.jpg)

4. ***golang实现字典树***
   
   - ```go
     // Trie 树结构
     type Trie struct {
         root *trieNode     // 根节点指针
     }
     
     // Trie 树节点
     type trieNode struct {
         char string                    // Unicode 字符
         isEnding bool                  // 是否是单词结尾
         children map[rune]*trieNode    // 该节点的子节点字典
     }
     
     // 初始化 Trie 树
     func NewTrie() *Trie {
         // 初始化根节点
         trieNode := NewTrieNode("/")
         return &Trie{trieNode}
     }
     
     // 初始化 Trie 树节点
     func NewTrieNode(char string) *trieNode  {
         return &trieNode{
             char: char,
             isEnding: false,
             children: make(map[rune]*trieNode),
         }
     }
     
     // 往 Trie 树中插入一个单词
     func (t *Trie) Insert(word string) {
         node := t.root   // 获取根节点
         for _, code := range word {  // 以 Unicode 字符遍历该单词
             value, ok := node.children[code]  // 获取 code 编码对应子节点
             if !ok {
                 // 不存在则初始化该节点
                 value = NewTrieNode(string(code))
                 // 然后将其添加到子节点字典
                 node.children[code] = value
             }
             // 当前节点指针指向当前子节点
             node = value
         }
         node.isEnding = true  // 一个单词遍历完所有字符后将结尾字符打上标记
     }
     
     // 在 Trie 树中查找一个单词
     func (t *Trie) Find(word string) bool {
         node := t.root
         for _, code := range word {
             value, ok := node.children[code] // 获取对应子节点
             if !ok {
                 // 不存在则直接返回
                 return false
             }
             // 否则继续往后遍历
             node = value
         }
         if node.isEnding == false {
             return false // 不能完全匹配，只是前缀
         }
         return true // 找到对应单词
     }
     ```

