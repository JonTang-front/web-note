### git特点
- git是一个分布式版本控制系统；
- git管理的不是最新版本的文件快照，而是把代码仓库完整地镜像下来，每一次的克隆操作，实际上都是一次对代码仓库的完整备份；
- git仓库中保存的是文件的所有版本数据，git工作目录只是从git仓库的压缩数据库中提取出来的某个独立版本内容，放在磁盘上供你试用和修改，git暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中；
- 对git而言，文件只有两种状态：已跟踪和未跟踪。添加到git管理的文件是已跟踪文件，未添加到git管理的文件是未跟踪文件，例如新创建的文件；
- 对用户而言，文件有以下几种状态：新文件，未修改，已修改和已放入暂存区。新文件：新创建的文件，未添加到git管理，git能检测到新文件，但无法跟踪新文件的修改。未修改：git跟踪的文件（曾经提交过）没有被修改。已修改：git跟踪的文件被修改了，但是没有添加到暂存区。已放入暂存区：git跟踪的文件被修改过或者新文件，并且通过add命令添加到了暂存区，待提交的文件。

### git版本管理下的文件生命周期

```
$ mkdir folder
##新建文件，添加文件内容

$ git add <filename>
##添加到git跟踪（staged暂存区）
##或者
$ git add .
##添加所有新建文件到git跟踪（staged暂存区）

$ git commit -am 注释
##提交修改文件到git管理

$ git pull
##从远程分支拉取文件

$ git push
##推送文件到远程分支
```
### git常用命令
```
$ git config key value
##系统配置，如git config --global user.name xxx设置用户名
```
   

```
$ git config --list
##查看系统配置信息
```

```
$ git --help
##查看git试用手册
```

```
$ git init
##创建git仓库（获取git仓库方式一）
```

```
$ git clone url [别名]
##拷贝现存的git仓库（获取git仓库方式二）
```

```
$ git add <filename>
##  添加文件到git暂存区（添加git跟踪），文件名可以是一个具体的文件，也可以是通配符，如*.txt等，或者“.”添加所有新加的文件到git管理
```
  

```
$ git commit -m "注释"
## 提交暂存区中的文件，不在暂存区中的文件不会被提交，加--amend覆盖上一次的提交
```
```
$ git commit -am "注释"
##把git跟踪的文件添加到暂存区，并且提交 
```

```
$ git status -s
##查看文件状态，-s表示精简信息
```

```
$ git diff
##查看尚未暂存的文件更新内容，-cached 查看已暂存的文件更新内容
```

```
$ git rm <filename>
##从git中移除文件并从工作目录中删除文件。如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母）。如果文件从 Git 仓库中删除（亦即从暂存区域移除，移除git跟踪），但仍然希望保留在当前工作目录中，使用--cached选项
```

```
$ git mv <sourcename> <targetname>
##文件重命名，相当于mv README.md README git rm README.md git add README三条命令的效果
```

```
$ git log
##查看提交历史
```

- -p
按补丁格式显示每个更新之间的差异。
- --stat
显示每次更新的文件修改统计信息。
- --shortstat
只显示 --stat 中最后的行数修改添加移除统计。
- --name-only
仅在提交信息后显示已修改的文件清单。
- --name-status
显示新增、修改、删除的文件清单。
- --abbrev-commit
仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
- --relative-date
使用较短的相对时间显示（比如，“2 weeks ago”）。
- --graph
显示 ASCII 图形表示的分支合并历史。
- --pretty
使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。

```
$ git reset HEAD <file>
##把文件从暂存区移出（取消文件暂存）
```

```
$ git checkout <branchname>
##切换分支
```
### git远程仓库
远程仓库是指托管在因特网或其他网络中的你的项目的版本库。

- git clone url/简写 别名 - 拷贝远程仓库
- origin - Git 给你克隆的仓库服务器的默认名字
-  git remote - 查看远程仓库及简写（远程仓库URL的简写，可以用URL拷贝远程仓库，也可以用简写拷贝）
-  git remote add <简写> <url> - 添加远程仓库
-  git fetch [remote-name] - 拉取远程仓库中的所有数据，但是不会自动合并或修改你当前的工作， 当准备好时你必须手动将其合并入你的工作，一般不用此命令
-  git pull - 从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支
-  git push [remote-name] [branch-name] - 推送到远程仓库
-  git remote show [remote-name] - 查看远程仓库更多信息
- git remote rename [old-name] [new-name] - 远程仓库重命名
- git remote rm [remote-name] - 移除一个远程仓库
- git config --global alias.别名 命令 - git命令设置别名

```
$ git config --global alias.ci commit   git ci 等价于 git commit
$ git config --global alias.st status   git st 等价于 git status
```
### git分支

```
$ git checkout -b dev
##创建分支并切换到新创建的分支
##相当于
$ git branch dev
$ git checkout dev

$ git branch
##查看所有分支

$ git checkout master
##切换分支

$ git merge <name>
##合并分支到当前分支
##例如在master分支下执行命令，则合并<name>分支到master分支

$ git branch -d <name>
##删除分支
```
