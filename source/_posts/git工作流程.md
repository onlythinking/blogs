---
title: Git工作流程
date: 2018-12-28 14:45:06
tags:
  - git
categories: 
  - 开发工具
copyright:
top: 2
typora-root-url: ../
typora-copy-images-to: ../images
---

## 1. 简介

1. 安装

   参考官网 [Git](https://git-scm.com/)

2. 基本配置

   配置Git 全局用户名和邮箱

   git config --global user.name  "xxx"

   git config --global user.email "xxx@email.com"

   git config --list 查看配置信息

## 2. 快速开始

1. Git 分工作空间（work dir），暂存区间（index），版本库（objects），远程仓库(remote)

   ​	![git-work-flow](/images/git-work-flow.png)

   - 创建本地新的存储库

     ```bash
     git init	
     ```

   - 添加文件到暂存区间

     ```bash
     git add <files>
     ```

   - 将暂存区文件提交到版本库

     ```bash
     git commit -m 'Add file.'
     ```
   - 将本地提交推送到远程仓库

     ```bash
     git push <remote url>ush <remote url>
     ```


2. 区分几个命令：

	​	git clone 将远程仓库完整克隆到本地磁盘

	​	git fetch 更新远程仓库最新版本信息到本地版本库

	​	git pull  更新远程仓库最新版本信息并且更新当前工作区间文件

3. Git 管理下文件的几种状态：

	​	已暂存 已修改 已提交

	​	![git-file-status](/images/git-file-status.png)



## 3. 分支管理和标签

 1. 分支管理命令

    ```bash
    git branch --list # 罗列本地所有分支
    git branch --all  # 罗列本地和远程所有分支
    git branch -r     # 罗列远程所有分支
    git branch -v     # 显示各分支最后提交信息
    git checkout <branch name> # 切换分支
    git checkout -b <new branch name> # 创建新分支
    git push origin <new branch name> # 推送新分支到远程
    git checkout -m <old branch> <new branch> # 重命名分支名称
    git branch -d <[list]branch name> # 删除本地分支
    git push origin --delete <branch name> # 删除远程分支
    ```

  2. 分支合并

    git  merge  rebase 是状态同步过程冲突的解决方式。

    1. 远程同步本地。

       ```none
                          origin/dev
                              |
                +−−−− (E)−−−−(F)
               /                 
       (A) −− (B)−−−−−−− (C) −− (D)  
                                 |          
                                dev
       ```

       远程分支新增 E F 提交记录，本地分支存在 C D 提交执行 git pull（默认冲突方式为merge） 远程更新后产生新Merge提交（如下图）

       ```none
                          origin/dev
                              |        
                +−−−− (E)−−−−(F)−−−−−−−−−−−−−−−−
               /                                \
       (A) −− (B) −−−−−−−−(C) −− (D)−−−−(G − merge commit)  
                                                 |          
                                                dev 
       ```

       我们使用 git pull-rebase 方式，rebase的时候本地提交 C D 被重新排序，而该期间不会产生新的提交。

       ```none
       (A) −− (B)−−−−−−− (E) −− (F) −−−−−−− (C') −− (D') 
                                 |                   |
                             origin/dev             dev
       ```

3. 标签（Tag）

   ​       标签主要用于代码归档，利于回滚。

   1. 标签管理命令

   2. ```bash
      git tag -l # 罗列本地所有标签
      git show <tag name> # 显示指定标签
      git tag -a v_1.0.0 -m "备注" # 创建标签
      git push origin <tag name> # 推送标签到远程
      git tag -d <tag name> # 删除本地标签
      git push --delete origin <tag name> # 删除远程标签
      ```

## 4. 工作流程

​	初始化项目创建 master dev test 三个分支​	

```
(A) −− (B) -> origin/dev    # 开发主分支      
(A) −− (B) -> origin/test   # 测试主分支             
(A) −− (B) -> origin/master # 生产归档主分支
```

​	以上三个分支伴随项目整个生命周期

```
	     + (E) -> feature/login 
		/
(A) −− (B) -- (C) -- (D) -> dev

		 + (E) -> hotfix
	    /		
(A) −− (B) -> master  
```

​	feature 分支用于功能开发中遇到需要分叉的情况，一般在功能开发完成后删除。操作命令：

```bash
git checkout -b feature/login # 创建 feature/login 分支
git rebase dev  # 同步 dev
git checkout dev # 切换到dev分支
git merge feature/login # merge 或者 rebase feature/login
```

​	hotfix 分支主要用于bug修复

```bash
git checkout -b hotfix # 创建 hotfix 分支
git rebase master # 同步 master 
git checkout hotfix # 切换 hotfix 分支
git merge hotfix # merge 或者 rebase hotfix
git tag -a <version no> -m "" # 归档一个标签
```



## 5. 问题

- 对比变化的文件

  ​	![git-diff](/images/git-diff.png)

  ```bash
  git diff <file> # 查看工作空间和暂存取件指定文件变化
  git diff <commit> # 查看指定提交变化
  git diff <commit> <commit> # 比对两个提交变化
  git diff --cached # 查看暂存区间和版本库变化
  ```

- 回退问题

  撤销方式：checkout revert reset，假如我需要撤销一个 "bad_commit" 的提交。 

  - git checkout 撤销 （这种方式是通过切换新分支的方式达到撤销的目的）

    ```bash
    git log --oneline # 查看提交历史
    git checkout <pre_commit> # 切换到 “bad_commit”提交之前
    git checkout -b <new branch> # 该分支就不包含 “bad_commit” 
    ```

  - git revert 撤销 （这种方式会追加一个撤销提交在后面）

    ```bash
    git revert <bad_commit> # 撤销指定提交
    ```

  - git reset

    - git reset --hard   重置暂存和工作空间，（即，之前提交内容将丢失）。
    - git reset --mixed 重置暂存空间但不重置工作空间（即，保留更改的文件但未标记为提交）并报告尚未更新的内容，默认为该选项。
    - git reset --soft  不会重置暂存和工作空间（即，保留所有改动文件）。