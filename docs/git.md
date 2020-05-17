# git 学习

[toc]



```shell
git branch -avv  // 所有 分支详细信息  本地分支和远程分支的关联信息

```

## 分支管理

```shell
#1.基础当前分支 创建当前分支
 git branch dev
 
#2.基于一个分支创建一个分支
 git branch test dev
#3.  基于远程分支创建一个分支
git branch  test2 origin/test2
#4. 基于一个提交创建分支
git branch test3 787998676

#5. 删除分支
git branch -d a b c
#6. 切换分支
git checkout a

```

## 冲突解决

```shell
# 解决冲突
git add -a;git commit -am "7 finish"

```

## 添加远程仓库

```
git remote add upstram http://111
```

## [Spring AOP](https://shimo.im/docs/Nj0bcFUy3SYyYnbI/read)

 