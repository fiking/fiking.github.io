---
title: leetcode-513
tags:
  - leetcode
  - 算法
categories:
  - 算法
  - leetcode
date: 2021-01-08 08:40:47
description:
---

### BFS

开始做BFS的题目， 题目链接： https://leetcode-cn.com/problems/find-bottom-left-tree-value/

 <!-- more -->

#### 1. 想到的是标记层数，记录最新一层的的第一个，最后一次记录即为需要的。
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        queue<TreeNode*> work;
        work.push(root);
        map<TreeNode*, int> dis;
        dis[root] = 0;

        int cur = 0;
        int ans = root->val;
        while (!work.empty()) {
            TreeNode* tmp = work.front();
            work.pop();
            if (dis[tmp] != cur) {
                ans = tmp->val;
                cur = dis[tmp];
            }

            if (tmp->left != nullptr) {
                work.push(tmp->left);
                dis[tmp->left] = dis[tmp] + 1;
            }

            if (tmp->right != nullptr) {
                work.push(tmp->right);
                dis[tmp->right] = dis[tmp] + 1;
            }
        }

        return ans;
    }
};
```
#### 2. 参考评论代码，从右往左遍历，最后一个即为需要的。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        queue<TreeNode*> work;
        work.push(root);

        TreeNode* ans = root;
        while (!work.empty()) {
            ans = work.front();
            work.pop();
            if (ans->right != nullptr) {
                work.push(ans->right);
            }

            if (ans->left != nullptr) {
                work.push(ans->left);
            }
        }

        return ans->val;
    }
};
```

