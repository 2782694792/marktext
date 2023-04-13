## 1、创建 tag

- 添加一个 tag，并推送到远程仓库，tag 的命名会根据版本号进行约束

```git
// 本地创建 tag 轻量标签
// git tag t3.11.3

// 本地创建 tag 附注标签
git tag -a t3.11.3 -m "release t3.11.3"

// 将 tag 推送到远程服务器
git push origin t3.11.3
```

轻量标签包含信息比较少，一般用附注标签。

## 2、删除 tag

如果 tag 不存在，可以先 `git pull` 或者 `git fetch`

```git
// 本地删除 tag 标签
git tag -d t3.11.3

// 远程删除 tag 标签
git push origin --delete t3.11.3
// 或者
git push origin :refs/tags/t3.11.3
```

## 3、查看 tag

```git
// 默认列表
git tag

// 查看 tag list列表
git tag --list

// -l 后通配符查询 tag 列表
git tag -l "t.3.*"

// 具体 tag 标签信息
git tag showname
```

## 4、在某个 commit 上打 tag

```git
git tag test_tag c809ddbf83939a89659e51dc2a5fe183af384233
git push origin test_tag
```

## 5、根据 tag 创建分支

```git
// 1. 获得最新
git origin fetch
// 2. 根据 tag 创建新分支 newbranch
git branch newbranch 3.11.3
// 3. 切换到新分支
git checkout newbranch
// 4. 将本地创建的分支提交到远程仓库
git push origin newbranch
```

创建完分支之后，有修改信息之后要进行推送到远程仓库，并且代码需要合并到其他分支。

```git
// 将 b3.11.3 合并到当前分支
git merge b3.11.3
```

> 如果分支名称跟标签名称一致，需进行如下 merge :

```git
// 合并分支
git merge refs/heads/b3.11.3

// 合并 tag 标签
git merge refs/tags/b3.11.3
```
