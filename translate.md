近期，XX大盗发现XXX富人区可行窃。这个富人区只有一个入口，我们称之为“根”，并且每栋房子有且只有一个“父”房子与之相连。经过一番侦查，XX大盗发现“这个地方的房屋布局类似于一个二叉树”。如果两个直接相连的房子在同一天晚上被盗，房屋会自动报警。
请计算在不触发警报的情况下，XX大盗最多一晚能够盗取的最高金额。
示例：
输入: [2,5,7,1,3,null,2]

     2
    / \
   5   7
  / \   \
 1   3   2

输出: 12
解释: XX大盗一晚能够盗取的最高金额 = 5 + 7 = 12.

class Main {
    /**
     * Definition for a binary tree node.
     */
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int val) {
            this.val = val;
        }
    }
    
    public int rob(TreeNode root) {

    }
}


解法一：暴力递归
public int rob(TreeNode root) {
    if (root == null) return 0;

    int money = root.val;
    if (root.left != null) {
        money += (rob(root.left.left) + rob(root.left.right));
    }

    if (root.right != null) {
        money += (rob(root.right.left) + rob(root.right.right));
    }

    return Math.max(money, rob(root.left) + rob(root.right));
}

问题：速度太慢 --> 爷爷在计算自己能偷多少钱的时候，同时计算了 4 个孙子能偷多少钱，也计算了 2 个儿子能偷多少钱。这样在儿子当爷爷时，就会产生重复计算一遍孙子节点。
解决方式：记忆化

解法二：记忆化递归 – 解决重复子问题
二叉树不适合拿数组当缓存，所以使用哈希表来存储结果，TreeNode 当做 key，能偷的钱当做 value。
public int rob(TreeNode root) {
    HashMap<TreeNode, Integer> memo = new HashMap<>();
    return robInternal(root, memo);
}

public int robInternal(TreeNode root, HashMap<TreeNode, Integer> memo) {
    if (root == null) return 0;
    if (memo.containsKey(root)) return memo.get(root);
    int money = root.val;

    if (root.left != null) {
        money += (robInternal(root.left.left, memo) + robInternal(root.left.right, memo));
    }
    if (root.right != null) {
        money += (robInternal(root.right.left, memo) + robInternal(root.right.right, memo));
    }
    int result = Math.max(money, robInternal(root.left, memo) + robInternal(root.right, memo));
    memo.put(root, result);
    return result;
}
问题：仍有性能损耗，需要更大的内存空间来缓存TreeNode


解法三：动态规划
动态规划里面的最优子结构：4 个孙子偷的钱 + 爷爷的钱 VS 两个儿子偷的钱 哪个组合钱多，就当做当前节点能偷的最大钱数。

任何一个节点能偷到的最大钱的状态可以定义为
1.	当前节点选择不偷：当前节点能偷到的最大钱数 = 左孩子能偷到的钱 + 右孩子能偷到的钱
2.	当前节点选择偷：当前节点能偷到的最大钱数 = 左孩子选择自己不偷时能得到的钱 + 右孩子选择不偷时能得到的钱 + 当前节点的钱数

public int rob(TreeNode root) {
    //定义一个数组，用来表示当前节点是偷还是不偷
    int[] res = robInterval(root);
    return Math.max(res[0], res[1]);
}

private int[] robInterval(TreeNode root) {
    if(root == null){
        return new int[2];
    }
    int[] res = new int[2];
    int[] left = robInterval(root.left);
    int[] right = robInterval(root.right);
    //表示当前节点不抢劫
    res[0] = Math.max(left[1],left[0]) + Math.max(right[0], right[1]);
    //当前节点抢劫
    res[1] = left[0] + right[0] + root.val;
    return  res;
}




