# 1、config 配置

    `git config` 命令用于**获取并设置存储库或全局选项**。这些变量可以控制 Git 的外观和操作的各个方面。

```git
git config [<file-option>] [type] [--show-origin] [-z|--null] name [value [value_regex]]

git config [<file-option>] [type] --add name value
git config [<file-option>] [type] --replace-all name value [value_regex]
git config [<file-option>] [type] [--show-origin] [-z|--null] 
    --get name [value_regex]
git config [<file-option>] [type] [--show-origin] [-z|--null] 
    --get-all name [value_re5gex]
git config [<file-option>] [type] [--show-origin] [-z|--null] [--name-only] 
    --get-regexp name_regex [value_regex]
git config [<file-option>] [type] [-z|--null] --get-urlmatch name URL
git config [<file-option>] --unset name [value_regex]
git config [<file-option>] --unset-all name [value_regex]
git config [<file-option>] --rename-section old_name new_name
git config [<file-option>] --remove-section name
git config [<file-option>] [--show-origin] [-z|--null] [--name-only] 
    -l | --list
git config [<file-option>] --get-color name [default]
git config [<file-option>] --get-colorbool name [stdout-is-tty]
git config [<file-option>] -e | --edit
```

## 1.1 描述

- 查询 / 设置 / 替换 / 取消设置选项。该名称实际上是由点 ( `.` ) 分隔键，该值将被转义；

- `--add` 将多行添加到选项。更新或取消设置多行可能出现的选项，则需要给出 POSIX 正则表达式 `value_regex` ；

- 类型说明符可以是 `--int` 或 `--bool`，以使 `git config` 确保变量是给定类型，或者 `--path`，它进行一些路径扩展。如果没有类型说明符，则不执行检查或转换；

- 读取时，**默认从系统，全局和存储库本地配置文件读取值**，而选项`--system`，`--global`，`--local`和`--file <filename>`可用于告知命令从只有那个位置设置和读取；

- 写入时，**默认将新值写入存储库本地配置文件**，并且可以使用选项`--system`，`--global`，`--file <filename>`来告诉命令写入的位置( 默认值`--local`)；

- 错误代码：
  
  | 代码  | 含义                             |
  | --- | ------------------------------ |
  | 0   | 0 成功，非 0 失败                    |
  | 1   | 部分或键无效                         |
  | 2   | 没有提供部分或名称                      |
  | 3   | 配置文件无效                         |
  | 4   | 配置文件无法写入                       |
  | 5   | 尝试取消设置不存在的选项、尝试取消设置/设置多个行匹配的选项 |
  | 6   | 尝试使用无效的正则表达式                   |

## 1.2 存储位置

1. `/etc/gitconfig` ：包含了适用于**系统所有用户和所有库的值**，通过 `—system` 读写；

2. `~/.gitconfig` ：具体到你的**用户**，通过 `--global` 读写；

3. `.git/config` ：每个级别重写前一个**级别**的值。覆盖了在`/etc/gitconfig`中的同一个值。

## 1.3 配置用户和密码

    使得 Git 将总是会使用以下信息来处理在系统中所做的一切操作：

```git
$ git config --global user.name "m"
$ git config --global user.email "y.com@gmail.com"
```

> 若希望在不同项目使用不同信息，则取消 --global 选项。

## 1.4 配置编辑器

    缺省情况下，Git使用系统的缺省编辑器，通常可能是 `vi` 或者 `vim` ，若使用 emacs：

```git
$ git config --global core.editor emacs
```

## 1.5 配置比较工具

    缺省的比较工具用来解决合并时的冲突，如使用 vimdff：

```git
$ git config --global merge.tool vimdiff
```

## 1.6 检查配置

    列出Git可以在该处找到的所有的设置:

```git
$ git config --list
# 查看特定值
$  git config user.name
$  git config user.email
```

## 1.7 添加 / 删除配置项

- **添加**`-–add`：
  
  格式：`git config [–local|–global|–system] –add section.key value`

```git
$ git config -–add site.name y
```

- **删除**`-–unset`：
  
  格式：`git config [–local|–global|–system] –unset section.key`

```git
$ git config --local -–unset site.name
```

## 1.8 获取帮助

    获得任何 git 命令的手册页(manpage)帮助信息:

```git
$ git help <verb>
$ git <verb> --help
$ man git-<verb>

# 例
$ git help config
```

# 2、help 帮助

    显示有关Git的帮助信息。

```git
$ git help [-a|--all] [-g|--guide]
         [-i|--info|-m|--man|-w|--web] [COMMAND|GUIDE]
```

## 2.1 描述

- `--all` / `-a` ：标准输出上打印所有可用的命令；

- `--guide` / `-g` ：列出有用的 Git 指南；

- `git --help ...` 与 `git help` 相同，前者被内部转换为后者；

- `git help git` ：显示 git 手册页。

> 所有这些配置变量应该使用 `--global` 标志设置，例如：
> 
> ```git
> $ git config --global help.format web
> $ git config --global web.browser firefox
> ```

# 3、init 初始化

    **创建**一个空的 Git 仓库**或重新初始化**一个现有仓库。

```git
$ git init [-q | --quiet] [--bare] [--template=<template_directory>]
      [--separate-git-dir <git dir>]
      [--shared[=<permissions>]] [directory]
```

## 3.1 描述

- 创建一个具有`objects` 、`refs/head` 、`refs/tags` 和模板文件的 `.git` 目录，还创建了引用主分支的`HEAD`初始的一个`HEAD`文件；

- 如果通过 `$GIT_OBJECT_DIRECTORY` 环境变量指定了对象存储目录，那么将在下面创建 `sha1` 目录，否则将使用默认的 `$GIT_DIR/objects` 目录；

- 现有存储库中运行`git init`命令是安全的。 它**不会覆盖**已经存在的东西。**重新运行**`git init`的主要原因是**拾取新添加的模板**（`--separate-git-dir`，移动存储库）。

# 4、add 添加

    将**文件内容添加到索引**(将修改添加到**暂存区**)。也就是将要提交的文件的信息添加到索引库中。

```git
$ git add [--verbose | -v] [--dry-run | -n] [--force | -f] [--interactive | -i] [--patch | -p]
      [--edit | -e] [--[no-]all | --[no-]ignore-removal | [--update | -u]]
      [--intent-to-add | -N] [--refresh] [--ignore-errors] [--ignore-missing]
      [--chmod=(+|-)x] [--] [<pathspec>…​]
```

## 4.1 描述

- 将提交的文件信息添加到索引库（修改添加到暂存区），准备**为下一次提交分段**；通过选项，可以用于添加内容，只对工作树文件进行更改，或删除其中不存在的路径；

- 多次执行，可将任何新的或修改的文件添加到索引；

- 默认情况下，`git add` 不会添加忽略的文件，可以用`-f(force)`选项**添加被忽略的**文件。

## 4.2 基本用法

- 通过`git add <path>`的形式把`<path>`添加到索引库中，`<path>`可以是文件也可以是目录：

```git
$ git add <path>

-A : 
将所有跟踪文件和所有未跟踪文件添加到索引库
-u ：
将所有跟踪文件中添加到索引库
-i ：
查看路径中被所有修改过或已删除文件但没有提交的文件， 
并通过其 revert 子命令可以查看 <path> 中所有未跟踪的文件，同时进入一个子命令系统
```

- ` . ` : 将所有修改添加到暂存区；

- ` * ` : Ant 风格添加修改；

- ` *Controller ` : 将以 Controller 结尾的文件的所有修改添加到暂存区；

- ` Hello* ` : 将所有以Hello开头的文件的修改添加到暂存区；

- ` Hello? ` : 将以Hello开头后面只有一位的文件的修改提交到暂存区。

# 5、clone 克隆

    将存储库克隆到新目录中。

```git
$ git clone [--template=<template_directory>]
      [-l] [-s] [--no-hardlinks] [-q] [-n] [--bare] [--mirror]
      [-o <name>] [-b <name>] [-u <upload-pack>] [--reference <repository>]
      [--dissociate] [--separate-git-dir <git dir>]
      [--depth <depth>] [--[no-]single-branch]
      [--recurse-submodules] [--[no-]shallow-submodules]
      [--jobs <n>] [--] <repository> [<directory>]
```

## 5.1 描述

- 将存储库克隆到新目录中，为克隆的存储库中的每个分支创建远程跟踪分支，并将存储库作为当前活动分支的初始分支：
  
  > `git branch -r` 可见远程分支

- 没有参数的 `git pull` 将会**将远程主分支合并到当前主分支**；

- 默认配置通过在 `refs/remotes/origin` 下创建对远程分支头的引用，并通过初始化 `remote.origin.url` 和 `remote.origin.fetch` 配置变量来实现；

## 5.2 基本用法

```git
$ git clone http[s]://example.com/repo.git
$ git clone http://git.oschina.net/sample.git
$ git clone ssh://repo.git
$ git clone git://repo.git
$ git clone /opt/project.git 
$ git clone file://project.git
$ git clone ftp[s]://repo.git
$ git clone rsync://repo.git

$ git clone [user@]example.com:path/to/repo.git
```

    通常来说，**Git 协议下载速度最快**，SSH 协议用于需要用户认证的场合。

- 在当前目录中使用克隆，而无需检出：

```git
git clone -l -s -n . ../copy
```

- 创建一个**裸存储库**以将您的更改发布给公众：

```git
$ git clone --bare -l /home/proj/.git /pub/scm/proj.git
```

# 6、status 状态

    用于**显示工作目录和暂存区的状态**，不显示已经 `commit` 到项目历史中去的信息。

```git
$ git status [<options>…​] [--] [<pathspec>…​]
```

## 6.1 描述

- 显示索引文件和当前HEAD提交之间的差异；

- 输出的内容分为3个分类 / 组：
  
  ```git
  # Changes to be committed:  (已经在stage区, 等待添加到HEAD中的文件)
  # Changes not staged for commit: (有修改, 但是没有被添加到stage区的文件)
  # Untracked files:(没有tracked过的文件, 即从没有add过的文件)
  ```

- 通过 `git status -uno` 可以只列出所有**已经被** git **管理**的**且被修改但没提交**的文件：
  
  ```git
  $ git status -uno
  ```

## 6.2 忽略文件

    在一个特殊的文件 `.gitignore` 中把要忽略的文件放在其中， 每一个想忽略的文件应该独占一行, `*` 这个符号可以作为通配符使用。

```git
# 路径
directoryName
# 文件
a.txt
*.exe
```

# 7、diff 更改

    用于**显示提交和工作树等之间的更改**。此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是**修改之后还没有暂存起来的变化内容**。

```git
git diff [options] [<commit>] [--] [<path>…​]
git diff [options] --cached [<commit>] [--] [<path>…​]
git diff [options] <commit> <commit> [--] [<path>…​]
git diff [options] <blob> <blob>
git diff [options] [--no-index] [--] <path> <path>
```

## 7.1 描述

- 在工作树和索引或树之间显示更改，索引和树之间的更改，两个树之间的更改，两个blob 对象之间的更改或两个文件在磁盘上的更改。

```git
git diff <file> # 比较当前文件和暂存区文件差异 git diff

git diff <id1><id1><id2> # 比较两次提交之间的差异

git diff <branch1> <branch2> # 在两个分支之间比较

git diff --staged # 比较暂存区和版本库差异

git diff --cached # 比较暂存区和版本库差异

git diff --stat # 仅仅比较统计信息  
```

## 7.2 基本用法

- 检查**工作树**：

```git
$ git diff            # 工作树中的更改尚未分段进行下一次提交
$ git diff --stat     # 查看简单的 diff 结果
$ git diff --cached   # 索引和最后一次提交之间的变化，以及 add 但没有 commit
$ git diff HEAD       # 自上次提交以来工作树中的更改
```

- 直接将**两个分支上最新的提交**做 diff：

```git
$ git diff topic master
$ #或 
$ git diff topic..master

# master分支上的变更
$ git diff topic...master

# 查看当前目录和另外一个分支(test)的差别
$ git diff test

# 显示当前目录下的lib目录和上次提交之间的差别
$ git diff HEAD -- ./lib
```

- 查看 **已经暂存起来的文件(staged)和上次提交时的快照之间(HEAD)** 的差异：

```git
$ git diff --cached
$ git diff --staged
```

- 显示**工作版本(Working tree)和HEAD**的差别:

```git
$ git diff HEAD

# 比较上次提交和上上次提交
$ git diff HEAD^ HEAD
```

- 比较**两个历史版本之间**的差异：

```git
$ git diff SHA1 SHA2
```

# 8、commit 提交

用于将更改记录(提交)到存储库。将索引的当前内容与描述更改的用户和日志消息一起存储在新的提交中。

```git
git commit [-a | --interactive | --patch] [-s] [-v] [-u<mode>] [--amend]
       [--dry-run] [(-c | -C | --fixup | --squash) <commit>]
       [-F <file> | -m <msg>] [--reset-author] [--allow-empty]
       [--allow-empty-message] [--no-verify] [-e] [--author=<author>]
       [--date=<date>] [--cleanup=<mode>] [--[no-]status]
       [-i | -o] [-S[<keyid>]] [--] [<file>…​] 
```

## 8.1 描述

- 通过使用 `git add` 对索引进行递增的“添加”更改；

- 将文件作为参数列出到 `git commit`命令，提交将忽略索引中分段的更改，而是记录列出的文件的当前内容；

- 带有 `-a` 选项的 `git commit`命令来自动从**所有已知文件**中添加 “更改”；

- 使用 `--interactive / -i` 或 `--patch / -p` 选项与 `git commit` 命令一起以**交互方式**将工作树的修改内容或索引和工作树之间选择补丁块作为提交的一部分；

- 使用 [git reset](http://www.yiibai.com/git/git_reset.html "git reset") 命令恢复。

> 一个文件只能在索引中恢复，而不是在工作树中，使用 `git reset HEAD - <file>` 进行上一次提交的恢复。

# 9、reset 复位

    用于将当前 `HEAD` **复位到指定状态**。一般用于**撤销**之前的一些操作(如：`git add`,`git commit`等)。

```git
git reset [-q] [<tree-ish>] [--] <paths>…​
git reset (--patch | -p) [<tree-ish>] [--] [<paths>…​]
git reset [--soft | --mixed [-N] | --hard | --merge | --keep] [-q] [<commit>] 
```

## 9.1 描述

- 在第一和第二种形式中，将条目从 `<tree-ish>` 复制到索引。

- 在第三种形式中，将当前分支头( `HEAD` )设置为 `<commit>`，可选择修改索引和工作树进行匹配。

- 所有形式的 `<tree-ish>/<commit>` 默认为 `HEAD` 。

## 9.2 基本用法

    将当前的**分支重设**到指定的 commit 或者 HEAD（默认），并根据 [mode] 有可能更新索引或工作目录。

> [mode] : `hard`、`soft`、`mixed`、`merged`、`keep`

```git
git reset [--hard|soft|mixed|merge|keep] [<commit>或HEAD]
```

- `--hard`：重设(reset) 索引和工作目录，自从`<commit>` 以来在工作目录中的任何改变都被丢弃，并把HEAD指向 `<commit>`。

### 9.2.1 回滚添加操作

```git
$ edit    file1.c file2.c           # (1) 
$ git add file1.c file1.c           # (1.1) 添加两个文件到暂存
$ mailx                             #  (2) 
$ git reset                           # (3) 
$ git pull git://info.example.com/ nitfol    # (4) 自动合并，改变仍在工作目录
```

### 9.2.2 回滚最近一次提交

```git
$ git commit -a -m "这是提交的备注信息"
$ git reset --soft HEAD^      #(1) HEAD^ 表示指向 HEAD 之前最近的一次提交
$ edit code                        #(2) 编辑代码操作
$ git commit -a -c ORIG_HEAD  #(3)
```

> `git commit` 命令中 `-a`参数的意思是告诉git，自动把所有修改的和删除的文件都放进暂存区，未被git跟踪的新建的文件不受影响。
> 
> `commit`命令中`-c <commit>` 或者 `-C <commit>`意思是拿已经提交的对象中的信息(作者，提交者，注释，时间戳等)提交。

### 9.2.3 回滚最近几次提交

```git
$ git branch topic/wip     (1) 
$ git reset --hard HEAD~3  (2) 回滚最近三次提交(删除 HEAD, HEAD^, HEAD~2)
$ git checkout topic/wip   (3)
```

### 9.2.4  回滚 pull

```git
$ git pull                         (1) 
$ git reset --hard                 (2) 
$ git pull . topic/branch          (3) 将 topic/branch 分支合并到当前的分支
                                        ，合并后的更改自动提交
$ git reset --hard ORIG_HEAD       (4) 把reset之前的HEAD放入.git/ORIG_HEAD文件中*
```

### 9.2.5 回滚 merge

```git
$ git reset --merge ORIG_HEAD      (2)    
```

`git reset --hard ORIG_HEAD` 回滚到 pull 之前的状态，同时清空工作区 `add` 的改变，为了避免丢弃工作区的内容，将 `--hard` 改成 `--merge` ，这样可以避免丢失。

### 9.2.7 中断的工作流程处理

    在工作分支 feature 下，当正在修改文件内容但又未完全处理完时，需要到另一分支 master 上去修改其他文件所存在的 bug ，我们可以这么做：

```git
# 工作分支下暂时先提交修改
$ git checkout feature ;# you were working in "feature" branch and 
$ work work work       ;# got interrupted 
$ git commit -a -m "snapshot WIP"                 (1) 
# 切换指定分支进行修改并提交
$ git checkout master 
$ fix fix fix 
$ git commit ;# commit with real log 
# 回到原来的工作分支，进行回滚、清理索引
$ git checkout feature 
$ git reset --soft HEAD^ ;# go back to WIP state  (2) 
```

### 9.2.8 重置单独的文件

    当已经添加了一个文件进入索引，而后又不打算提交文件时：

```git
$ git reset -- frotz.c                      (1) 从索引中去除 
```

### 9.2.9 保留工作区并丢弃之前的提交

```git
$ git tag start 
$ git checkout -b branch1 
$ edit 
$ git commit ...                            (1) 当前分支下提交
$ edit 
$ git checkout -b branch2                   (2) 发现不该为 branch1 分支，切换
$ git reset --keep start                    (3) 撤销 start 之后的提交，工作区不变
```

# 10、rm 删除

    用于从工作区和索引中**删除**文件。

    简介:

```git
git rm [-f | --force] [-n] [-r] [--cached] [--ignore-unmatch] 
         [--quiet] [--] <file> …
```

## 10.1 描述

- 从索引中删除文件，或从工作树和索引中删除文件，仅仅是删除了物理文件，没有删除记录；

- 当给出`--cached`时，暂存区内容必须与分支的提示或磁盘上的文件相匹配，从而仅将文件从索引中删除；

- `-am` 删除大批文件记录。

# 11、mv 移动命名

    用于**移动或重命名**文件，目录或符号链接。

    简介：

```git
git mv <options>… <args>…
```

## 11.1 描述

```git
# 重命名 <source>（必须存在） 为 <destination>
git mv [-v] [-f] [-n] [-k] <source> <destination>
# 最后一个参数必须是现有的目录; 给定的源(<source>)将被移动到这个目录中。
git mv [-v] [-f] [-n] [-k] <source> ... <destination directory>
```

# 12、branch 分支

    用于**列出，创建或删除分支**。

    简介：

```git
git branch [--color[=<when>] | --no-color] [-r | -a]
    [--list] [-v [--abbrev=<length> | --no-abbrev]]
    [--column[=<options>] | --no-column] [--sort=<key>]
    [(--merged | --no-merged) [<commit>]]
    [--contains [<commit]] [--no-contains [<commit>]]
    [--points-at <object>] [--format=<format>] [<pattern>…​]
git branch [--set-upstream | --track | --no-track] [-l] [-f] <branchname> [<start-point>]
git branch (--set-upstream-to=<upstream> | -u <upstream>) [<branchname>]
git branch --unset-upstream [<branchname>]
git branch (-m | -M) [<oldbranch>] <newbranch>
git branch (-d | -D) [-r] <branchname>…
git branch --edit-description [<branchname>]
```

## 12.1 描述

- `--list`，列出现有的分支，当前分支以星号突出显示；

- `-a`，显示本地和远程分支；

- `-r`，导致远程跟踪分支被列出；

- `--contains`，仅显示包含命名提交的分支；

- `<pattern>`，被用作一个 shell 通配符，将输出限制为匹配的分支。提供 `<pattern>`时，必须使用 `--list`; 否则命令被解释为分支创建。

## 12.2 基本用法

- 创建远程分支：
  
  ```shell
  $ git remote add new-remote-repo https://b.git
  $ git push <new-remote-repo> crazy-experiment~
  ```

- 修改分支名：
  
  ```shell
  $ git branch -m dev2 version.2
  # 列出
  $ git branch -r
  ```

- 删除远程分支：
  
  ```shell
  $ git push origin --delete dev2
  $ git push origin :crazy-experiment
  
  # 删除本地分支
  git branch -d crazy-experiment
  ```

- 合并某个分支到当前分支：
  
  ```git
  # 合并分支：version.2到当前分支(master)
  $ git merge version.2
  ```

# 13、checkout 切换恢复

    用于**切换分支或恢复工作树**文件。

```git
git checkout [-q] [-f] [-m] [<branch>]
git checkout [-q] [-f] [-m] --detach [<branch>]
git checkout [-q] [-f] [-m] [--detach] <commit>
git checkout [-q] [-f] [-m] [[-b|-B|--orphan] <new_branch>] [<start_point>]
git checkout [-f|--ours|--theirs|-m|--conflict=<style>] [<tree-ish>] [--] <paths>…​
git checkout [-p|--patch] [<tree-ish>] [--] [<paths>…]
```

## 13.1 描述

- 更新工作树中的文件以匹配索引或指定树中的版本；

- 若没给出路径，将会更新 `HEAD` ，将指定的分支设置为当前的分支。

## 13.2 基本用法

- 将文件还原为两个修订版本，取回错误删除的文件：

```git
$ git checkout master             #(1) 
$ git checkout master~2 Makefile  #(2) 从另一个提交中取出文件
$ rm -f hello.c
$ git checkout hello.c            #(3) 从索引中恢复 hello.c
```

- 检索所有的 `C` 源文件（引号）：

```git
# 引号指明
git checkout -- '*.C'
# 非引号则可能是分支名，则会进行分支切换
git checkout mytopic
```

# 14、merge 合并

    用于将两个或两个以上的开发历史加入（合并）一起。

    简介：

```git
git merge [-n] [--stat] [--no-commit] [--squash] [--[no-]edit]
    [-s <strategy>] [-X <strategy-option>] [-S[<keyid>]]
    [--[no-]allow-unrelated-histories]
    [--[no-]rerere-autoupdate] [-m <msg>] [<commit>…​]
git merge --abort
git merge --continue
```

## 14.1 描述

- 将来自命名提交的更改（从其历史从当前分支转移到当前分支之后）；

- 合并来自另一个存储库的更改，可以手动使用将更改偶从一个分支合并到另一个分支。 

## 14.2 基本用法

- 合并分支 fixes 和 enhancements 在当前分支的顶部：

```git
git merge fixes enhancements
```

- 合并 obsolete 分支到当前分支，使用 `ours` 合并策略：

```git
get merge -s ours obsolete
```

- 合并 maint 分支到当前分支，但不自动进行新的提交：

```git
git merge --no-commit maint
# 去掉 --no-commit 则进行自动新的提交 
```

# 15、mergetool 合并冲突解决工具

    用于运行合并冲突解决工具来解决合并冲突。

```git
git mergetool [--tool=<tool>] [-y | --[no-]prompt] [<file>…]
```

## 15.1 描述

- 通常在git合并后运行；

- 若有一个或多个 <file> 参数，则将用来解决每个文件的差异（跳过无冲突）；

- 指定目录将包含该路径中所有未解析文件；

- 若没指定，则将在每个有合并冲突的文件上运行。

## 15.2 基本用法

- 可以设置 *BeyondCompare*, *DiffMerge* 等作为git的比较和合并的可视化工具,方便操作；

- 下载并安装 BeyondCompare, DiffMerge 等，这里以 *BeyondCompare* 为例：

```git
#difftool 配置  
git config --global diff.tool bc4  
git config --global difftool.bc4.cmd "\"c:/program files (x86)/beyond compare 4/bcomp.exe\" \"$LOCAL\" \"$REMOTE\""


#mergeftool 配置  
git config --global merge.tool bc4
git config --global mergetool.bc4.cmd  "\"c:/program files (x86)/beyond compare 4/bcomp.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\""  
git config --global mergetool.bc4.trustExitCode true

#让git mergetool不再生成备份文件(*.orig)  
git config --global mergetool.keepBackup false
```

- 比较当前修改情况：

```git
git difftool HEAD
```

# 16、log 日志

    用于显示提交日志信息。

    简介：

```git
git log [<options>] [<revision range>] [[\--] <path>…]
```

## 16.1 描述

- 采用适用于 `git rev-list` 命令的选项来控制显示的内容；

- 适用于 `git diff- *` 命令的选项，以控制如何更改每个提交引入的内容。

## 16.2 基本用法

- 显示整个提交历史记录，同时跳过合并：

```git
git log --no-merges
```

- 显示自 `v2.6.12` 版以来所有提交更改 `include/scsi` 或 `drivers/scsi` 子目录中的任何文件的所有提交：

```git
git log master include/scsi drivers/scsi
```

- 显示最近两周的更改文件 `gitk` （ -- 避免分支混淆）:

```git
git log --since="2 weeks ago" --gitk
```

- 显示“`test`”分支中尚未在“`release`”分支中的提交，以及每个提交修改的路径列表：

```git
git log --name-status release..test
```

- 显示更改 `builtin/rev-list.c` 的提交，包括文件名称设立之前发生的提交：

```git
git log --follow builtin/rev-list.c
```

- 显示在任何本地分支中的所有提交，但不包括任何远程跟踪分支机构的起始点：

```git
git log --branches --not --remotes=origin
```

- 显示本地主服务器中的所有提交，但不显示任何远程存储库主分支：

```git
git log master --not-remote=*/master
```

- 显示历史，包括变化差异，但仅从“主分支”的角度来看，忽略来自合并分支的提交，并显示合并引入的变化的完全差异：

```git
git log -p -m --first-parent
```

- 显示文件 main.c 中的函数 main( ) 随着时间推移的变化：

```git
git log -L '/int main/',/^}/:main.c
```

- 根据 ID 查询日志：

```git
$ git log commit1_id commit2_id #查询commit1与commit2之间的记录，包括两者
$ git log commit1_id..commit2_id #同上，但是不包括commit1

HEAD代表最后一次提交，HEAD^为最后一个提交的父提交，
等同于HEAD～1，HEAD～2代表倒数第二次提交
--pretty按指定格式显示日志信息,
可选项有：oneline,short,medium,full,fuller,email,raw以及format:
默认为medium，可以通过修改配置文件来指定默认的方式
```

> 常见的 format 选项：
> 
> ```git
> #选项     #说明
> %H      提交对象(commit)的完整哈希字串
> %h      提交对象的简短哈希字串
> %T      树对象(tree)的完整哈希字串
> %t      树对象的简短哈希字串
> %P      父对象(parent)的完整哈希字串
> %p      父对象的简短哈希字串
> %an     作者(author)的名字
> %ae     作者的电子邮件地址
> %ad     作者修订日期(可以用 -date= 选项定制格式)
> %ar     作者修订日期，按多久以前的方式显示
> %cn     提交者(committer)的名字
> %ce     提交者的电子邮件地址
> %cd     提交日期
> %cr     提交日期，按多久以前的方式显示
> %s      提交说明
> ```

举例：

```git
$ git log --pretty=format:"%an %ae %ad %cn %ce %cd %cr %s" --graph
# 指定作者的提交
$ git log --author=maxsu
$ git log --before={3,weeks,ago} --after={2018-04-18}
```

- `--since`，`--affter` - 仅显示指定时间之后的提交(不包含当前日期)；

- `--until`，`--before` - 仅显示指定时间之前的提交(包含当前日期)

# 17、stash 储藏

    用于将更改储藏在脏工作目录中。

    简介：

```git
git stash list [<options>]
git stash show [<stash>]
git stash drop [-q|--quiet] [<stash>]
git stash ( pop | apply ) [--index] [-q|--quiet] [<stash>]
git stash branch <branchname> [<stash>]
git stash save [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
         [-u|--include-untracked] [-a|--all] [<message>]
git stash [push [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
         [-u|--include-untracked] [-a|--all] [-m|--message <message>]]
         [--] [<pathspec>…​]]
git stash clear
git stash create [<message>]
git stash store [-m|--message <message>] [-q|--quiet] <commit>
```

## 17.2 描述

- 当要记录工作目录和索引的当前状态，但想要返回干净的工作目录时，使用 `git stash` 保存本地修改，并恢复工作目录以匹配 `HEAD` 提交；

- `list` 列出所储藏的修改，`show` 进行检查，`apply` 恢复，默认情况下，储藏列表”分支名称上的 WIP “，但可以在创建一个消息时在命令行上给出具体描述性的消息；

- 创建的最新储藏存储在 `refs/stash` 中，这个引用中的反垃圾中会发现较旧的垃圾邮件，并且可以使用通常的 reflog 语法命名（`stash@{0}`是最近创建的垃圾邮件），也可以通过指定存储空间索引来引用锁存。

## 17.3 基本用法

- 拉取到一颗肮脏树

    当发现本地的更改与上游的更改冲突时，`git pull` 拒绝覆盖您的更改，此时可以隐藏更改，执行 `git pull` ，再解压缩：

```git
 git pull
 ...
file foobar not up to date, cannot merge.
$ git stash
$ git pull
$ git stash pop
```

- 工作流中断

    当要处理紧急的事务从而不得不中断自己的工作流程时，应该提交临时分支来存储更改并返回原始分支进行紧急修复：

```git
$ git checkout -b my_wip                // 临时分支
$ git commit -a -m "WIP"                // 临时提交
$ git checkout master                   // 返回主分支
$ edit emergency fix # 编辑内容          // 紧急修复
$ git commit -a -m "Fix in a hurry"     // 提交修复
$ git checkout my_wip                   // 返回临时分支
$ git reset --soft HEAD^                // 移位值最新的提交的头位置
```

    可使用 `git stash` 来简化：

```git
$ git stash        // 临时储藏
$ edit             // 处理
$ git commit -a -m 'fix'        // 紧急处理
$ git stash pop    // 获取最近一次储藏的内容，之后删除栈中对应的 stash
```

- 储藏工作

    当修改了文件后，想要切换分支但不想提交当前进行的工作，此时可以往栈中推送新的储藏：

```git
$ git stash
Saved working directory and index state \
  "WIP on master: 049d078 added the index file"
HEAD is now at 049d078 added the index file
(To restore them type "git stash apply")

# 查看现有的储藏
$ git stash list
```

- 应用存储

    若想应用之前的变更，可以通过 `apply` 进行指定应用，不指定将会使用最近的存储：

```git
$ git stash apply
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   index.html
#      modified:   lib/simplegit.rb
--重新暂存被暂存的文件
$ git stash apply --index
--移除储藏的文件
$ git stash drop stash@{0}
```

- 取消储藏

    通过取消该储藏的补丁使得取消之前所储藏的修改：

```git
$ git stash show -p stash@{0} | git apply -R
# 不指定储藏的索引将会选择最近的储藏
-- 设置别名
$ git config --global alias.stash-unapply '!git stash show -p | git apply -R'
$ git stash apply
$ #... work work work
$ git stash-unapply
```

- 储藏中创建分支

    当储藏了一些工作之后继续工作，重新储藏将会归并冲突，若想重新检验储藏变更，创建新分支，成功将会丢弃储藏：

```git
$ git stash branch testchanges
```

# 18、tag 标签

    用于创建，列出，删除或验证使用GPG签名的标签对象。

    简介：

```git
git tag [-a | -s | -u <keyid>] [-f] [-m <msg> | -F <file>]
    <tagname> [<commit> | <object>]
git tag -d <tagname>…
git tag [-n[<num>]] -l [--contains <commit>] [--no-contains <commit>]
    [--points-at <object>] [--column[=<options>] | --no-column]
    [--create-reflog] [--sort=<key>] [--format=<format>]
    [--[no-]merged [<commit>]] [<pattern>…]
git tag -v [--format=<format>] <tagname>…
```

## 18.1 描述

- 创建标签可以：用于在开发阶段，某个阶段的完成，创建一个版本，在开发中都会使用到，指向软件开发中的一个关键时期；

- 不能创建已经存在的标签，除非指定 `-f`；

- 创建标签对象以及一个标签消息：`-a` 、`-s` 或 `-u <keyid>`，使用 `-m <msg>` 或 `-F <file>`，否则将启动编辑器自主输入标签消息；

- 标签类型：
  
  - 轻量级标签，像是不会变化的分支，实际上它就是个指向特定提交对象的引用。
  
  - 含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。

## 18.2 基本用法

- 列出已有的标签，也可进行筛选：

```git
$ git tag
$ git tag -l 'v1.4.2.*'
```

- 创建含附注类型的标签：

```git
$ git tag -a v1.4 -m 'my version 1.4'

# 轻量级标签
$ git tag v1.4-lw

# git show 查看响应标签的版本信息，包括标签对象
--$ git show v1.4
tag v1.4
Tagger: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Feb 9 14:45:11 2009 -0800

my version 1.4

commit 15027957951b64cf874c3557a0f3547bd83b3ff6
Merge: 4a447f7... a6b4c97...
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sun Feb 8 19:02:46 2009 -0800
```

- 签署标签：

    如果有自己的私钥，还可用 GPG 来签署标签：

```git
$ git tag -s v1.5 -m 'my signed 1.5 tag'
```

- 删除标签：

```git
$ git tag -d v1.0    
```

- 验证已经签署的标签：

```git
$ git tag -v v1.4.2.1
object 883653babd8ee7ea23e6a5c392bb739348b1eb61
type commit
tag v1.4.2.1
tagger Junio C Hamano <junkio@cox.net> 1158138501 -0700

GIT 1.4.2.1
```

- 后期标签加注：

```git
$ git tag -a v1.2 9fceb02
```

- 分享标签：

    默认情况下不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。运行 `git push origin [tagname]` ：

```git
$ git push origin --tags
Counting objects: 50, done.
Compressing objects: 100% (38/38), done.
Writing objects: 100% (44/44), 4.56 KiB, done.
Total 44 (delta 18), reused 8 (delta 1)
To git@github.com:schacon/simplegit.git
 * [new tag]         v0.1 -> v0.1
 * [new tag]         v1.2 -> v1.2
 * [new tag]         v1.4 -> v1.4
 * [new tag]         v1.4-lw -> v1.4-lw
 * [new tag]         v1.5 -> v1.5
```

# 19、fetch 获取

    用于从另一个存储库中下载对象和引用。

    简介：

```git
git fetch [<options>] [<repository> [<refspec>…]]
git fetch [<options>] <group>
git fetch --multiple [<options>] [(<repository> | <group>)…]
git fetch --all [<options>]
```

## 19.1 描述

- 从一个或多个其他存储库中获取分支或标签（引用）以及完成其历史所必需的对象；

- 远程跟踪以更新，需要将这些更新获取回本地；

- 默认情况下还会获取正在获取的历史记录的任何标签；

- 可以从单个命名的存储库或 URL 中，也可以从多个存储库中获取；

- 获取的参考名称以及它们所指向的对象名称被写入到`.git/FETCH_HEAD`中。

## 19.2 基本用法

- 更新远程跟踪分支：

```git
# 从远程 refs/heads/ 命名空间复制所有分支，并存储到本地 refs/remotes/origin/ 中
$ git fetch origin
```

- 使用 refspec：

```git
# 从远程分支 pu 和 maint 提取到本地 pu 和 tmp
$ git fetch origin +pu:pu maint:tmp
```

- 远程分支上窥视：

```git
$ git fetch git://git.kernel.org/pub/scm/git/git.git maint
# 使用FETCH_HEAD来检查具有git-log的分支
$ git log FETCH_HEAD 
```

- 更新远程分支主机：

```git
$ git fetch <远程主机名>
# 更新所有分支
$ git fetch
# 指定远程主机的指定分支
$ git fetch <远程主机名> <分支名>
# 查看远程分支
$ git branch -a
* master    # 本地的当前分支
  remotes/origin/master    # 远程分支
```

# 20、pull 整合

    用于从另一个存储库或本地分支获取并集成（整合）到本地分支。

    简介：

```git
git pull [options] [<repository> [<refspec>…]]
```

## 20.1 描述

- 默认模式下，`git pull`是`git fetch`后跟`git merge FETCH_HEAD`的缩写；

- `git pull`使用给定的参数运行`git fetch`，并调用`git merge`将检索到的分支头合并到当前分支中。

## 20.2 基本用法

- **将远程主机的分支与本地的分支 master 合并**：

```git
$ git pull origin next:master
# 合并当前分支则省略 : 冒号
```

- **建立追踪关系（tracking）下 pull 可以省略远程分支名**：

```git
# 手动建立追踪关系
$ git branch --set-upstream master origin/next
# 合并
$ git pull origin
# 若当前分支只有一个跟踪分支，则可以省略远程主机名
$ git pull
# 采用 rebase 模式
$ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
```

## 20.3 与 fetch 的区别

1. git fetch ：相当于是从远程获取最新到本地，不会自动合并；

```git
$ git fetch origin master            # 下载最新版本都本地
$ git log -p master..origin/master   # 比较分支差别
$ git merge origin/master            # 合并
```

2. git pull ：相当于是从远程获取最新并 merge 到本地；

```git
git pull origin master
```

# 21、push 更新推送

    用于将本地分支的更新，推送到远程主机。格式与 pull 相似。

    简介：

```git
$ git push <远程主机名> <本地分支名>:<远程分支名>

git push [--all | --mirror | --tags] [--follow-tags] [--atomic] [-n | --dry-run] [--receive-pack=<git-receive-pack>]
       [--repo=<repository>] [-f | --force] [-d | --delete] [--prune] [-v | --verbose]
       [-u | --set-upstream] [--push-option=<string>]
       [--[no-]signed|--sign=(true|false|if-asked)]
       [--force-with-lease[=<refname>[:<expect>]]]
       [--no-verify] [<repository> [<refspec>…]]
```

## 21.1 描述

- 使用本地引用更新远程引用，同时发送完成给定引用所需的对象；

- 可以在每次推入存储库时，通过在那里设置挂钩触发一些事件；

- 当不指定使用`<repository>`参数时，将查询当前分支的 `branch.*.remote` 以此确定位置。

## 21.2 基本用法

- **删除远程分支**：

```git
# 推送本地空的分支
$ git push origin :master
# 等同于
$ git push origin --delete master
```

- **`-u` 指定默认主机**：

```git
$ git push -u origin master    # 后面可直接使用 git push
```

- **更改默认推送方式 `simple` 方法（ V2.0 之前默认 matching ）**：

```git
$ git config --global push.default matching
# 或者
$ git config --global push.default simple
```

- **推送更新**

```git
# 推送所有远程主机
$ git push --all origin
# 强制推送，产生一个 非直进式 合并
$ git push --force origin
# 推送标签，默认不推送
$ git push origin --tags
# 将远程分支推送到远程同名下
$ git push origin HEAD
# 将当前分支推送到源存储库中的远程引用匹配主机
$ git push origin HEAD:master
```

- **异分支推送、补充与覆盖**：

```git
# 推送新分支
$ git push origin lbranch-1:refs/rbranch-1
# 补充原有分支
$ git rebase rbranch-1
$ git push origin lbranch-2:refs/rbranch-1
# 覆盖远程分支
$ git push -f origin lbranch-2:refs/rbranch-1
--或者
$ git push origin :refs/rbranch-1   //删除远程的rbranch-1分支
$ git push origin lbranch-1:refs/rbranch-1
```

- **查看 push 结果**：

```git
$ gitk rbranch-1
```

- **标签推送与删除**：

```git
$ git push origin tag_name
# 删除远程标签
$ git push origin :tag_name
```

# 22、remote 管理

    用于管理一组跟踪的存储库。

    简介：

```git
git remote [-v | --verbose]
git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
git remote rename <old> <new>
git remote remove <name>
git remote set-head <name> (-a | --auto | -d | --delete | <branch>)
git remote set-branches [--add] <name> <branch>…​
git remote get-url [--push] [--all] <name>
git remote set-url [--push] <name> <newurl> [<oldurl>]
git remote set-url --add [--push] <name> <newurl>
git remote set-url --delete [--push] <name> <url>
git remote [-v | --verbose] show [-n] <name>…​
git remote prune [-n | --dry-run] <name>…​
git remote [-v | --verbose] update [-p | --prune] [(<group> | <remote>)…​]
```

## 22.1 基本用法

- **列出存在的远程分支**：

```git
git remote
```

- **列出详细信息**：

```git
# --verbose 显示对应的克隆地址
$ git remote -v
```

- **添加新的远程并检出一个分支**：

```git
# taging 指代对应的仓库
$ git remote add staging git://git.kernel.org/.../gregkh/staging.git
# 列出分支
$ git remote
# 更新远程分支
$ git fetch staging 
# 查看分支关系
$ git branch -r
# 切换到远程分支
$ git checkout -b staging staging/master
```

- **模仿 clone ，但只跟踪选定分支**

```git
$ git remote add -f -t master -m master origin git://example.com/git.git/
$ git merge origin
```

# 23、submodule 管理子模块

    用于初始胡、更新或检出子模块。

    简介：

```git
git submodule [--quiet] add [<options>] [--] <repository> [<path>]
git submodule [--quiet] status [--cached] [--recursive] [--] [<path>…​]
git submodule [--quiet] init [--] [<path>…​]
git submodule [--quiet] deinit [-f|--force] (--all|[--] <path>…​)
git submodule [--quiet] update [<options>] [--] [<path>…​]
git submodule [--quiet] summary [<options>] [--] [<path>…​]
git submodule [--quiet] foreach [--recursive] <command>
git submodule [--quiet] sync [--recursive] [--] [<path>…​]
git submodule [--quiet] absorbgitdirs [--] [<path>…​]
```

## 23.1 使用场景

    当项目越来越多的时候，常常会需要提取一个公共的库给多个项目使用，此时会有：

1. 如何在 git 项目中导入库？

2. 库在其他项目中被修改了可以更新到远程代码库？

3. 其他库如何获取库的最新的提交？

4. 如何在 clone 的时候自动导入库？ 

## 23.2 描述

    submodule 是一个很好的多项目使用共同类库的工具。

- 允许类库项目为 repository，子项目为一个单独的 git 项目存在父项目中，子项目可以有自己的独立的`commit`，`push`，`pull`；

- 父项目以 Submodule 的形式包含子项目，父项目可以指定子项目 header；

- 父项目中的提交信息包含 Submodule 的信息，clone 父项目时可以把 Submodule 初始化。

## 23.3 基本用法

- **添加子模块**：
1. 添加名为 “DbConnector” 的库，默认情况下子模块会将子项目放到一个与仓库同名的目录：

```git
$ git submodule add http://github.com/chaconinc/DbConnector
```

2. 此时会新增两个文件，包括 `.gitmodules` 文件，该配置文件保存了 URL 与已经拉取的本地目录之间的映射：

```git
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   .gitmodules
    new file:   DbConnector

$ cat .gitmodules
[submodule "DbConnector"]
    path = DbConnector
    url = http://github.com/chaconinc/DbConnector
```

> 如果有多个子模块，该文件就会有多条记录；
> 
> 如果 `.gitignore` 文件一样受到版本控制；

- **克隆含有子模块的项目**：
1. 项目中有 DbConnector 目录，不过是空的，需要进行初始化：

```git
$ ls -la
drwxr-xr-x  13 schacon  staff  442 Sep 17 15:21 .git
-rw-r--r--   1 schacon  staff   92 Sep 17 15:21 .gitmodules
drwxr-xr-x   2 schacon  staff   68 Sep 17 15:21 DbConnector
...
```

2. `git submodule init` 用来初始化本地配置文件，而 `git submodule update` 则从该项目中抓取所有数据并检出父项目中列出的合适的提交：

```git
$ git submodule init
Submodule 'DbConnector' (http://github.com/chaconinc/DbConnector) registered for path 'DbConnector'
$ git submodule update
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
Submodule path 'DbConnector': checked out 'c3f01dc8862123d317dd46284b05b6892c7b29bc'
```

3. 也可以在 clone 时利用 `--recursive` 进行自动初始化并更新仓库中的每一个子模块： 

```git
$ git clone --recursive http://github.com/chaconinc/MainProject
```

- **删除 submodule**：

```git
# 不支持直接删除，需手动删除：
git rm --cached pod-library
rm -rf pod-library
rm .gitmodules
更改git的配置文件config:
vim .git/config
# 删除后进行提交远程服务器
git commit -a -m 'remove pod-library submodule'
```

# 24、show 显示

    用于显示各种类型的对象。

    简介：

```git
git show [options] <object>…​
```

## 24.1 描述

- 显示一个或多个对象（`blobs`，树，标签和提交）；

- 显示日志消息和文本差异，还以 `git diff-tree --cc` 生成的特殊格式呈现合并提交；

- 显示标签消息和引用对象；

- 显示树的名称（相当于 `git ls-tree` 和 `--name-only`）；

- 显示 blobs 的普通的内容；

- 通常用来显示提交引入的更改。

## 24.2 基本用法

- 显示标签及其指向对象：

```git
$ git show v1.0.0
```

- 显示标签指向的树：

```git
$ git show v1.0.0^{tree}
```

- 显示标签指向的提交的主题：

```git
$ git show -s --format=%s v1.0.0^{commit}
```

- 显示 README 文件内容，next 分支的第10次提交的内容：

```git
$ git show next~10:README
```

- 将 Makefile 的内容连接到分支主控的头部：

```git
$ git show master:Makefile master:t/Makefile
```

# 25、shortlog 汇总

    用于汇总 git 日志输出。

    简介：

```git
git log --pretty=short | git shortlog [<options>]
git shortlog [<options>] [<revision range>] [[\--] <path>…​]
```

    如果在命令行上没有传递修订版本，并且标准输入不是终端或没有当前的分支，则`git shortlog`将输出从标准输入读取的日志的摘要，而不引用当前存储库。

## 25.1 基本用法

- 显示用户提交次数以及提交的注释：

```git
$ git shortlog -s -n
  135  Tom Preston-Werner
  15  Jack Danger Canty
```

> `-s` 省略每次 commit 注释仅仅返回一个简单统计；
> 
> `-n` 按照 commit 数量从多到少进行排序。

# 26、describe 显示最近标签

    显示离当前提交最近的标签。

    简介：

```git
git describe [--all] [--tags] [--contains] [--abbrev=<n>] [<commit-ish>…​]
git describe [--all] [--tags] [--contains] [--abbrev=<n>] --dirty[=<mark>]
```

## 26.1 描述

- 查找从提交可访问的最新标记；

- 如果标签指向提交，则只显示标签。 否则，它将标记名称与标记对象之上的其他提交数量以及最近提交的缩写对象名称后缀；

- 默认情况下(不包括`--all`或`--tags`) git 描述只显示注释标签。

## 26.2 基本用法

- 显示标签描述信息：

```git
$ git describe --tags
tag1-2-g026498b
```

> `2`:表示自打tag `tag1` 以来有`2`次提交（commit）；
> 
> `g026498b`：g 为git的缩写，在多种管理工具并存的环境中很有用处；

# 27、rebase 合并修改

    在另一个分支基础之上重新应用，用于把一个分支的修改合并到当前分支。

    简介：

```git
git rebase [-i | --interactive] [options] [--exec <cmd>] [--onto <newbase>]
    [<upstream> [<branch>]]
git rebase [-i | --interactive] [options] [--exec <cmd>] [--onto <newbase>]
    --root [<branch>]
git rebase --continue | --skip | --abort | --quit | --edit-todo
```

## 27.1 示例

- **merge 合并：**
1. 基于远程分支 origin 新建一个分支 mywork：

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-01-36-image.png" title="" alt="" data-align="center">

2. 在 mywork 分支上做一些修改生成两个提交：

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-03-12-image.png" title="" alt="" data-align="center">

3. 此时用 pull 命令把 origin 分支上的修改拉下来与自己的修改进行合并

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-06-23-image.png" title="" alt="" data-align="center">

```git
# 1.
$ git checkout -b mywork origin
# 2.
$ vi file.txt
$ git commit
$ vi otherfile.txt
$ git commit
# 3.
$ git pull ...
```

- **rebase 合并：**
1. 若是想分支历史合并，使得远程记录进行合并，则用 rebase 合并：

```git
$ git checkout mywork
$ git rebase origin
```

    该命令会把”`mywork`“分支里的每个 commit 取消掉，并临时保存为补丁(patch 在”`.git/rebase`“)，然后把”`mywork`“ 更新 ”`origin`“ ，最后把补丁应用到”`mywork`“上。

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-10-00-image.png" title="" alt="" data-align="center">

    更细 mywork 之后，会指向新的提交，丢弃旧的提交。

> 运行垃圾收集命令(pruning garbage collection), 删除被丢弃的提交。

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-11-41-image.png" title="" alt="" data-align="center">

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-22-11-11-54-image.png" title="" alt="" data-align="center">

    rebase 过程中也会出现冲突，git 会停止 rebase，解决完冲突后，add 更新内容的索引，无需 commit ，只需要：

```git
$ git rebase --continue
```

    此时 git 会 apply 余下的 补丁。

    任何时候可以用 --abort 来终止 rebase 操作。并且 mywork 会回到 rebase 开始前的状态：

```git
$ git rebase --abort
```
