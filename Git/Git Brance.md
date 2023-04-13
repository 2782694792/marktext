## 第一章 基础篇

### 1、Git Commit

- Git 仓库中的提交记录保存的是目录下的所有快照，复制整个目录并粘贴那样；

- 条件允许下，会与上一个版本进行对比，并将所有的差异打包到一起作为一个提交记录；

- 保存了提交的历史记录，几乎每次都会有父节点；

#### 1.1 父节点

`C2` 的父节点是 `C1` ，父节点是当前提交中变更的基础。

```git
git commit
```

<img title="" src="file:///D:/Download/MarkText/workspace/image/2022-08-10-09-55-50-image.png" alt="" data-align="center">

### 2、Git Branch

- Git 分支知识简单的指向某个提交记录；

- `早建分支，多建分支`；

- **过多的分支不会造成存储或内存上的开销**，同时分支易于维护。

#### 2.1 新建分支

新创建的分支 `newIamge` 指向的是提交记录 `C1`。

```git
$ git branch newImage
# 查看分支
$ git branch
# 查看分支代码详情
$ git branch -vv
```

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-10-10-45-02-image.png" title="" alt="" data-align="center">

#### 2.2 分支提交

没有“在”分支上

```git
git commit
# 查看分支的提交情况
```

<img title="" src="file:///D:/Download/MarkText/workspace/image/2022-08-10-10-55-59-image.png" alt="" data-align="center">

#### 2.3 切换分支

提交修改之前先切换到新的分支上，修改才能保存到新的分支里

> 注意：
> 
>       在 Git 2.23 中，引入了一个名为 `git switch` 的新命令，最终会取代 `git checkout` ，因为 `git checkout` 作为单个命令有点超载，它承载了多个独立的功能。

```git
$ git checkout newImage; git commit
```

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-10-11-01-24-image.png" title="" alt="" data-align="center">

#### 2.4 创建新分支同时切换

```git
git checkout <branch-name>
```

### 3、Git Merge

合并：新建一个分支并开发新功能，开发完成后再合并回主线。

合并的第一种方法：`git merge` ，在 Git 中合并两个分支时会产生一个特殊的提交记录，它有两

    个父节点，目的就是为了 **把这两个父节点以及其祖先都包含进来** 。

#### 3.1 合并分支

合并前：

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-10-11-32-33-image.png" title="" alt="" data-align="center">

进行合并：

```git
git merge <branch-name>

$ git merge bugFix
```

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-10-11-33-13-image.png" title="" alt="" data-align="center">

把 main 分支合并到 bugFix：

```git
git checkout bugFix; git merge main
```

main 继承至 bugFix ，Git 只需要把 bugFix 移动到 main 所指向的那个提交记录。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-10-19-20-35-image.png" title="" alt="" data-align="center">

### 4、 Git rebase

Rebase：取出一系列的提交记录，“复制”他们，然后在另一个地方逐个放下去；

作用：创造更线性的提交历史。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-12-15-31-04-image.png" title="" alt="" data-align="center">

    当前所在 bugFix 工作状态下，实现对分支 bugFix 进行迁移至 main 分支上。

#### 4.1 分支迁移

```git
git rebase main
```

分支 bugFix 迁移到 main 分支的顶端，获得了更线性的提交序列，但提交记录 C3 依然存在，而新的 C3' 是 C3 的副本。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-12-15-32-44-image.png" title="" alt="" data-align="center">

将分支 main 进行迁移到 C3' 的工作状态下，前提进行状态更换 `git checkout main` ，再进行分支迁移 `git rebase bugFix` 。

<img src="file:///D:/Download/MarkText/workspace/image/2022-08-12-15-38-04-image.png" title="" alt="" data-align="center">

# 
