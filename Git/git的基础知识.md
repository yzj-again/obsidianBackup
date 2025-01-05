---
date: 2024-12-24
tags:
  - Git
---
## 代码托管平台

我们已经有了本地库，本地库可以帮我们进行版本控制，为什么还需要代码托管平台呢？

它的任务是帮我们维护远程库。

下面说一下本地库和远程库的交互方式，有一下两种：

1）团队内部协作

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224202050.png)

2）跨团队协作

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224202605.png)

### 托管中心分类

局域网环境下：可以搭建Gitlab服务器作为代码托管中心，Gitlib可以自己去搭建。

外网环境下：可以由Github或Gitee作为代码托管中心，Github或Gitee是现成的代码托管中心，不用自己去搭建。

## 初始化本地仓库

什么是版本库呢？版本库又名仓库（Repository），你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

所以，创建一个版本库非常简单，首先，选择一个合适的地方，创建一个空目录：

```bash
 git init
Initialized empty Git repository in /home/zijianye/Learning/LearnGit/demo1/.git/
```

瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个`.git`的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

如果你没有看到`.git`目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看见。

也不一定必须在空目录下创建Git仓库，选择一个已经有东西的目录也是可以的。

### 把文件添加到版本库

首先这里再明确一下，所有的版本控制系统，其实只能跟踪文本文件的改动，比如 `TXT` 文件，网页，所有的程序代码等等，Git也不例外。版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

不幸的是，Microsoft的Word格式是二进制格式，因此，版本控制系统是没法跟踪Word文件的改动的，前面我们举的例子只是为了演示，如果要真正使用版本控制系统，就要以纯文本方式编写文件。

因为文本是有编码的，比如中文有常用的 `GBK` 编码，日文有 `Shift_JIS` 编码，如果没有历史遗留问题，强烈建议使用标准的 `UTF-8` 编码，所有语言使用同一种编码，既没有冲突，又被所有平台所支持。

使用Windows的童鞋要特别注意：千万不要使用Windows自带的**记事本**编辑任何文本文件。原因是Microsoft开发记事本的团队使用了一个非常弱智的行为来保存UTF-8编码的文件，他们自作聪明地在每个文件开头添加了 `0xefbbbf`（十六进制）的字符，你会遇到很多不可思议的问题，比如，网页第一行可能会显示一个“?”，明明正确的程序一编译就报语法错误，等等，都是由记事本的弱智行为带来的。建议你下载 Visual Studio Code 代替记事本，不但功能强大，而且免费！

## git的常用操作

### add、commit和status命令

言归正传，现在我们编写一个`readme.txt`文件，内容如下：

```plain
Git is a version control system.
Git is free software.
```

一定要放到 `demo1` 目录下（子目录也行），因为这是一个Git仓库，放到其他地方Git再厉害也找不到这个文件。

我们可以使用 `git status` 查看暂存区状态：

```shell
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        readme.txt

nothing added to commit but untracked files present (use "git add" to track)
```

和把大象放到冰箱需要3步相比，把一个文件放到Git仓库只需要两步。

第一步，用命令 `git add` 告诉Git，把文件添加到暂存区：

```shell
$ git add readme.txt
```

执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。

我们可以用status命令查看暂存区和工作区：

```shell
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   readme.txt
```

第二步，用命令 `git commit` 告诉Git，把文件从暂存区提交到本地库：

```shell
$ git commit -m "wrote a readme.txt file"
[master (root-commit) 5c8c345] wrote a readme.txt file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

简单解释一下 `git commit` 命令，`-m` 后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

嫌麻烦不想输入 `-m "xxx"` 行不行？确实有办法可以这么干，但是强烈不建议你这么干，因为输入说明对自己对别人阅读都很重要。

`git commit` 命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。

为什么Git添加文件需要 `add`，`commit` 一共两步呢？因为 `commit` 可以一次提交很多文件，所以你可以多次 `add` 不同的文件，比如：

```plain
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

提交后，我们已让用status查看状态：

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

### 本地结构

工作区：就是你在电脑里能看到的目录，比如我的`learngit`文件夹就是一个工作区。

版本库：工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为 `stage`（或者叫index）的==暂存区==，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224201303.png)

> 必须使用git init命令创建仓库，执行git add . 和git commit（提交成功后）,再使用git branch命令，才显示出本地分支。
> 
> ```bash
> git branch ：查看本地分支
> git branch -a :查看本地及远程仓库的分支
> git branch --all ：查看本地及远程仓库的分支
> ```
>因为git的分支必须指向一个commit，没有任何commit就没有任何分支

前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用 `git add` 把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用 `git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个 `master` 分支，所以，现在，`git commit` 就是往 `master` 分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

俗话说，实践出真知。现在，我们再练习一遍，先对 `readme.txt` 做个修改，比如加上一行内容：

```plain
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
```

然后，在工作区新增一个 `LICENSE` 文本文件（内容随便写）。

先用`git status`查看一下状态：

```plain
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        LICENSE
no changes added to commit (use "git add" and/or "git commit -a")
```

Git非常清楚地告诉我们，`readme.txt` 被修改了，而 `LICENSE` 还从来没有被添加过，所以它的状态是 `Untracked`。

现在，使用两次命令 `git add` ，把 `readme.txt` 和 `LICENSE` 都添加后，用 `git status` 再查看一下：

```shell
$ git add readme.txt LICENSE
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   LICENSE
	modified:   readme.txt
$ git commit -m "modified readme,add LICENSE file"
[master 8b12f46] modified readme,add LICENSE file
 2 files changed, 2 insertions(+)
 create mode 100644 LICENSE
$ git status 
On branch master
nothing to commit, working tree clean
```

现在，暂存区的状态就变成这样了：    

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224212052.png)

### log命令

现在，你已经学会了修改文件，然后把修改提交到Git版本库，我们多练习几次 `add` 和 `commit` 操作。像这样，你不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态。有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为 `commit`。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个 `commit` 恢复，然后继续工作，而不是把几个月的工作成果全部丢失。

当然了，在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用 `git log` 命令查看：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224223135.png)

> 这里如果历史记录很多，在记录末尾有 `:` ，则可以使用快捷键查看：
> + `空格`：下一页
> + `b`：上一页
> + `q`：退出日志

`git log` 命令显示从最近到最远的提交日志，我们可以看到多次提交，最近的一次是修改readme文件，上一次是给readme添加内容，最早的一次是 `wrote a readme file`。

友情提示的是，你看到的一大串类似 `30962f34a0...` 的是 `commit id`（版本号），和SVN不一样，Git的 `commit id` 不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示，而且你看到的 `commit id` 和我的肯定不一样，以你自己的为准。为什么 `commit id` 需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面我们还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。

可以理解为 `30962f34a0...` 是当前历史记录对应的索引 `key` ，`value` 是历史记录对应的具体内容。

#### log的其他参数

常见的日志展示方式：

1. `git log --pretty=oneline`：减少输出信息，避免看得眼花缭乱的
    
    ![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224224057.png)
    
2. `git log --oneline`：比上面展示更简洁。
    
    ![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224224325.png)

3. `git reflog`：查看命令历史
    
    ![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224224701.png)

### reset命令

每提交一个新版本，实际上Git就会把它们自动串成一条时间线。如果使用可视化工具查看Git历史，就可以更清楚地看到提交历史的时间线。

好了，现在我们启动时光穿梭机，准备把 `readme.txt` 回退到上一个版本，也就是 `添加text/html和text/plain的区别` 的那个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`30962f3...` ，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`，或者可以直接用版本号进行回退操作。

现在，我们要把当前版本回退到上一个版本，就可以使用`git reset`命令：

```shell
$ git reset --hard HEAD^
HEAD is now at 4aa71af 添加text/html和text/plain的区别
```

查看readme的内容，果然被还原了。

还可以继续回退到上一个版本，不过且慢，让我们用`git log`再看看现在版本库的状态：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224225340.png)

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224225414.png)

最新的那个版本已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？

办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个对应版本的 commit id 是 `1094adb...`，于是就可以指定回到未来的某个版本：

```shell
$ git reset --hard 30962f3
HEAD is now at 83b0afe 修改git branchd的意义
```

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的 `HEAD` 指针，当你回退版本的时候，Git仅仅是把HEAD从指向 `修改git branchd的意义` 这个分支。

#### reset命令的其他参数

**git reset –soft** 先看具体操作：

```shell
# 初始状态 
$ echo "A" > file.txt 
$ git add file.txt 
$ git commit -m "A"

# 创建B提交 
$ echo "B" >> file.txt 
$ git add file.txt 
$ git commit -m "B" 

# 创建C提交 
$ echo "C" >> file.txt 
$ git add file.txt 
$ git commit -m "C" 

# 添加新的改动D到暂存区（但还未提交） 
$ echo "D" >> file.txt 
$ git add file.txt 

# 修改工作区内容（未commit、未add）
$ echo "E" >> file.txt

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

# 执行reset 9b4417cd
$ git reset --soft B

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt
        
$ git diff --staged        
diff --git a/file.txt b/file.txt
index 35d242b..8422d40 100644
--- a/file.txt
+++ b/file.txt
@@ -1,2 +1,4 @@
 A
 B
+C
+D        
# 此时： 
# 1. HEAD指向B 
# 2. 暂存区不会被清空（C的“提交”和D的暂存都存在） 
# 3. file.txt中包含"C"、"D"和"E"的内容（工作区不变）
```
可见我们虽然指向了对应分支（branch），但 file 文件内还是没有区别，并且会产生一个新的暂存区内容（==虽然我们回到了提交 B 的状态，但是 C 提交所引入的修改仍然存在于暂存区中，此时暂存区中包含了 C 提交所引入的修改 "C"==）。

会产生一个新的暂存内容"C"：

```shell
# 没有 D 和 E 对应的操作
# 执行reset 
git reset --soft B

git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file.txt
        
git diff --staged
diff --git a/file.txt b/file.txt
index 35d242b..b1e6722 100644
--- a/file.txt
+++ b/file.txt
@@ -1,2 +1,3 @@
 A
 B
+C
```

暂存区内容会进行覆盖，多次 add 后，该文件的最新修改会覆盖到暂存区。

> --soft：==只移动 HEAD 到指定的 commit，但保留原先暂存区和工作目录的内容，同时会将指定 commit 之后提交的内容设置到暂存区中。==
> 
> 可以用于压缩最近的提交（想撤销commit，但保留所有修改内容），用该命令将 HEAD 移动到一个旧一点的提交上(即你想要保留的第一个提交)，然后然后再 commit 就可以将多个最近的提交压缩为一个（当然，该功能也可以用 `git rebase -i` 实现）。

**git reset –-mixed**：先看具体操作

```shell
# 初始状态 
echo "A" > file.txt 
git add file.txt 
git commit -m "A"

# 创建B提交 
echo "B" >> file.txt 
git add file.txt 
git commit -m "B" 

# 创建C提交 
echo "C" >> file.txt 
git add file.txt 
git commit -m "C" 

# 添加新的改动D到暂存区（但还未提交） 
echo "D" >> file.txt 
git add file.txt 

# 修改工作区内容（未commit、未add）
echo "E" >> file.txt

git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

# 执行reset b8f7de2f
git reset --mixed B
Unstaged changes after reset:
M       file.txt

git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

no changes added to commit (use "git add" and/or "git commit -a")
# 此时： 
# 1. HEAD指向B 
# 2. 暂存区被清空（C的“提交”和D的暂存都被清除） 
# 3. 但file.txt中仍包含"C"、"D"和"E"的内容（在工作区）
```

可以看到，file.txt内容是 A 到 E，无论暂存区有什么内容（包括未提交的D），都会被清空，但这些改动会保留在工作区。

如果想保留这些改动：

```shell
git add file.txt    # 重新添加到暂存区
git commit -m "new changes"   # 提交
```

> --mixed：==移动 HEAD 到指定的 commit，同时重置暂存区为指定 commit 的状态(将内容从 HEAD 复制到暂存区中)，但保留原先的工作目录，同时将添加暂存区的修改撤销到工作目录中。该选项为默认选项，可以省略==
> 
> 有 **取消暂存文件** 效果（撤销commit和add）。实际上它与 git add 所做的操作相反，可以用该命令撤销 add 到暂存区的修改到工作区。

`--hard` 是撤销全部commit、add和工作区修改。

### diff命令

**Git diff** 命令是 Git 中非常重要的一个命令，它用于比较不同版本的文件之间的差异。通过这个命令，我们可以清晰地看到哪些内容被添加、删除或修改了。

基础用法：

+ **git diff**:
    + 不带任何参数时，默认比较==工作区（Working Directory）和暂存区（Staging Area）所有文件之间==的差异。
    + 显示了哪些文件被修改了，以及修改了哪些内容。
+ **git diff --cached**或**--staged**:
    + 比较暂存区和最近一次提交（HEAD）之间的差异。
    - 显示了哪些修改已经被添加到暂存区，但还没有提交。
- **git diff HEAD:**
    - 比较工作区和最近一次提交之间的差异。
    - 与 `git diff` 的作用相似，但更明确地指定了比较对象。

具体操作：

```shell
$ git add test3.txt
$ git commit -m "add test3"
[master ad32375] add test3
 1 file changed, 1 insertion(+)
 create mode 100644 test3.txt
$ git status
On branch master
nothing to commit, working tree clean
# 更改工作区内容，增加一行
$ git diff test3.txt
diff --git a/test3.txt b/test3.txt
index c008d76..0cc71a7 100644
--- a/test3.txt
+++ b/test3.txt
@@ -1 +1 @@
-add test3
+add test3 add second
$ git add test3.txt
$ git diff --staged
diff --git a/test3.txt b/test3.txt
index c008d76..0cc71a7 100644
--- a/test3.txt
+++ b/test3.txt
@@ -1 +1 @@
-add test3
+add test3 add second
```

> git是以行按照单位进行管理数据。

## git分支

分支就是科幻电影里面的平行宇宙，当你正在电脑前努力学习Git的时候，另一个你正在另一个平行宇宙里努力学习SVN。

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN！

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

其他版本控制系统如SVN等都有分支管理，但是用过之后你会发现，这些版本控制系统创建和切换分支比蜗牛还慢，简直让人无法忍受，结果分支功能成了摆设，大家都不去用。

但Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227225041.png)

### 创建分支与合并分支

在版本回退里，你已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即 `master` 分支。`HEAD` 严格来说不是指向提交，而是指向 `master`，`master` 才是指向提交的，所以，`HEAD` 指向的就是当前分支。

一开始的时候，`master` 分支是一条线，Git用 `master` 指向最新的提交，再用 `HEAD` 指向 `master`，就能确定当前分支，以及当前分支的提交点：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227230141.png)

每次提交，`master` 分支都会向前移动一步，这样，随着你不断提交，`master` 分支的线也越来越长。

当我们创建新的分支，例如 `dev` 时，Git新建了一个指针叫 `dev`，指向 `master` 相同的提交，再把 `HEAD` 指向 `dev`，就表示当前分支在 `dev` 上：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227230250.png)

你看，Git创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227230347.png)

假如我们在 `dev` 上的工作完成了，就可以把 `dev` 合并到 `master` 上。Git 怎么合并呢？最简单的方法，就是直接把 `master` 指向 `dev` 的当前提交，就完成了合并：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227230420.png)

所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241227230451.png)

基础用法：

+ **git branch**:
    + 不带任何参数时，默认==只列出分支名称，不显示详细信息==。
    + `-v` 参数表示显示本地所有分支及其最近一次提交的信息。
    + `-a` 参数表示显示所有本地和远程分支。
    + 后面跟一个名字，表示创建分支。
    + `-d` 参数表示删除本地分支。

切换分支：

```shell
# 切换到 branch01 分支
$ git switch branch01
# 创建一个新分支并切换到该分支
$ git switch -c new-branch

# 当前分支有未提交的更改，包括未暂存和未提交的更改，则会提示
$ git switch dev
error: Your local changes to the following files would be overwritten by checkout:
        test4.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

git switch 和 git checkout 主要区别

| 特点   | git switch | git checkout |
| ---- | ---------- | ------------ |
| 主要功能 | 切换分支       | 切换分支、恢复文件    |
| 语法   | 更简洁        | 相对复杂         |
| 专注度  | 只关注分支操作    | 同时处理分支和文件操作  |
| 引入版本 | Git 2.23   | 早期 Git 版本    |
>git switch 和 git checkout 主要区别
>1. 如果当前分支有未提交的更改，则 `git switch` 命令将拒绝切换，并提示您在切换分支之前提交或撤消更改；而 `git checkout` 命令将覆盖这些更改。因此，在使用 `git checkout` 命令切换分支之前，请确保已经提交或撤消了所有更改。
>1. `git checkout` 可以用于恢复文件，而 `git switch` 则不行

合并分支：

```shell
# 将dev分支的操作合并到master分支，必须先切回master分支
$ git merge dev
```

具体实现：

```shell
# 先查看当前版本库有哪些分支
$ git branch -v
* master ad32375 add test3
# 我们创建`dev`分支，然后切换到`dev`分支：
$ git branch dev
$ git switch dev
M       test3.txt
Switched to branch 'dev'
$ git branch
* dev
  master
$ vim test4.txt
$ git add test4.txt
$ git commit -m "add test4"
[dev 90fbc76] add test4
 1 file changed, 1 insertion(+)
 create mode 100644 test4.txt
 $ tree .
.
├── LICENSE
├── bak.txt
├── file.txt
├── readme.txt
├── test2.txt
├── test3.txt
└── test4.txt
0 directories, 7 files
# 从上面可见我们以及在dev分支创建了一个新文件，并写入了内容
# 切回master分支
$ git switch master
Switched to branch 'master'
$ tree . 
.
├── LICENSE
├── bak.txt
├── file.txt
├── readme.txt
├── test2.txt
└── test3.txt

0 directories, 6 files
$ git merge dev
Updating ad32375..90fbc76
Fast-forward
 test4.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test4.txt
# 此时master分支就有内容了，此时可以删除dev分支
$ git branch -d dev
Deleted branch dev (was 90fbc76).
```

### 解决冲突

人生不如意之事十之八九，合并分支往往也不是一帆风顺的。

准备新的 `feature1` 分支，继续我们的新分支开发：

```shell
$ git switch -c feature1
Switched to a new branch 'feature1'
```

修改`readme.txt`最后一行，改为：

```plain
Creating a new branch is quick AND simple.
```

在 `feature1` 分支上提交：

```shell
$ git add readme.txt

$ git commit -m "AND simple"
[feature1 14096d0] AND simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

切换到 `master` 分支：

```shell
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
```

在 `master` 分支上把 `readme.txt` 文件的最后一行改为：

```plain
Creating a new branch is quick & simple.
```

提交：

```shell
$ git add readme.txt 
$ git commit -m "& simple"
[master 5dc6824] & simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

现在，`master`分支和`feature1`分支各自都分别有新的提交，变成了这样：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228202230.png)

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：

```shell
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

果然冲突了！Git告诉我们，`readme.txt` 文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：

```plain
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

我们可以直接查看readme.txt的内容：

```plain
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

Git用 `<<<<<<< ======= 和 >>>>>>>` 标记出不同分支的内容，这里我们需要和另一分支的提交者沟通后，保留唯一提交，当我们修改后如下后保存：

```plain
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
Creating a new branch is quick and simple.
```

再提交：

```plain
$ git add readme.txt 
$ git commit -m "conflict fixed"
[master cf810e4] conflict fixed
```

现在，`master`分支和`feature1`分支变成了下图所示：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228202658.png)

用带参数的 `git log` 也可以看到分支的合并情况：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   cf810e4 (HEAD -> master) conflict fixed
|\  
| * 14096d0 (feature1) AND simple
* | 5dc6824 & simple
|/  
* b17d20e branch test
* d46f35e (origin/master) remove test.txt
* b84166e add test.txt
* 519219b git tracks changes
* e43a48b understand how stage works
* 1094adb append GPL
* e475afc add distributed
* eaadf4e wrote a readme file
```

最后，删除`feature1`分支：

```shell
$ git branch -d feature1
Deleted branch feature1 (was 14096d0).
```

工作完成。

###  分支管理策略

通常，合并分支时，如果可能，Git会用 `Fast forward` 模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用 `Fast forward` 模式，Git 就会在 merge 时生成一个新的 commit，这样，从分支历史上就可以看出分支信息。

下面我们实战一下 `--no-ff` 方式的 `git merge`：

首先，仍然创建并切换 `dev` 分支：

```plain
$ git switch -c dev
Switched to a new branch 'dev'
```

修改readme.txt文件，并提交一个新的commit：

```plain
$ git add readme.txt 
$ git commit -m "add merge"
[dev f52c633] add merge
 1 file changed, 1 insertion(+)
```

现在，我们切换回`master`：

```plain
$ git switch master
Switched to branch 'master'
```

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

```plain
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

合并后，我们用`git log`看看分支历史：

```plain
$ git log --graph --pretty=oneline --abbrev-commit
*   e1e9c68 (HEAD -> master) merge with no-ff
|\  
| * f52c633 (dev) add merge
|/  
*   cf810e4 conflict fixed
...
```

可以看到，不使用`Fast forward`模式，merge后就像这样：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228205255.png)

总结：
1. git merge默认使用的时“快进”（fast-forward）模式，所以 dev 合并到 master 分支时,Git 在合并两者时，只会简单地把指针右移，如下图：

    ![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228222435.png)
    
2. git merge --no-ff 代码合并，表示禁止使用快进模式，所以会**生成一个新的提交**：

    ![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228215941.png)
在 `--no-ff` 模式下进行回退：
在 main 分支进行回退一个版本操作，会撤销合并：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228220534.png)

在 dev 分支进行回退一个版本操作，发现合并历史依然存在：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228220913.png)

就算在 master 分支删除 dev 分支，提交记录依然存在：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228221427.png)

在没有参数 `--no-ff` 下进行回退：

无论在哪个分支，回退都是各自回退。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228222917.png)

在 master 分支删除 dev 分支，其他dev分支提交记录不可见了：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228223230.png)

### Bug分支

软件开发中，bug 就像家常便饭一样。有了 bug 就需要修复，在 Git 中，由于分支是如此的强大，所以，每个 bug 都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的 bug 的任务时，很自然地，你想创建一个分支 `issue-101` 来修复它，但是，等等，当前正在 `dev` 上进行的工作还没有提交：

```shell
$ git status
On branch dev
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   devWork.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

```

并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该 bug，怎么办？

幸好，Git还提供了一个 `stash` 功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```shell
$ git stash
Saved working directory and index state WIP on dev: 719f2e9 add dev work 2.
```

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228224614.png)

> git stash 只能缓存被跟踪的文件，本次新增的文件并不能被缓存，需要先 add 添加进暂存区之后才能被缓存。

现在，用 `git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```shell
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git switch -c issue-101
Switched to a new branch 'issue-101'
```

现在修复bug，需要把最后一行作者改成 master，然后提交：

```plain
$ git add readme.txt 
$ git commit -m "in branch issue-101 fix bug"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228225006.png)


修复完成后，切换到 `master` 分支，并完成合并，最后删除 `issue-101` 分支：

```plain
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'ort' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228225309.png)

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228225407.png)

太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到`dev`分支干活了！

```plain
$ git switch dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```

工作区是干净的，刚才的工作现场存到哪去了？用 `git stash list` 命令看看：

```plain
$ git stash list
stash@{0}: WIP on dev: 719f2e9 add dev work 2.
```

工作现场还在，Git把 `stash` 内容存在某个地方了（从图上可以看出是一个不同于提交的图标），但是需要恢复一下，有两个办法：

一是用 `git stash apply` 恢复，但是恢复后，`stash` 内容并不删除，你需要用 `git stash drop` 来删除；

另一种方式是用 `git stash pop`，恢复的同时把 `stash` 内容也删了：

```shell
$ git stash pop
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   devWork.txt
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (d31f9b9c11a803ea9246e3246d0cccd66b273d22)
```

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228225822.png)

再用 `git stash list` 查看，就看不到任何 `stash` 内容了，同时工作区和暂存区内容都回来了。

```plain
$ git stash list
```

你可以多次`stash`，恢复的时候，先用`git stash list`查看，然后恢复指定的`stash`，用命令：

```plain
$ git stash apply stash@{0}
```

在 `master` 分支上修复了bug后，我们要想一想，`dev` 分支是早期从 `master` 分支分出来的，所以，这个bug其实在当前 `dev` 分支上也存在。

那怎么在`dev`分支上修复同样的bug？重复操作一次，提交不就行了？

有木有更简单的方法？

有！

同样的bug，要在 `dev`上修复，我们只需要把`869da06: in branch issue-101 fix bug`这个提交所做的修改“复制”到 `dev` 分支。注意：我们只想复制 `869da06: in branch issue-101 fix bug` 这个提交所做的修改，并不是把整个 `master` 分支merge过来。

为了方便操作，Git专门提供了一个`cherry-pick` 命令，让我们能复制一个特定的提交到当前分支：

```shell
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
error: Your local changes to the following files would be overwritten by merge:
        readme.txt
Please commit your changes or stash them before you merge.
Aborting
fatal: cherry-pick failed
```

`cherry-pick` 执行时，当前分支的工作区和暂存区最好都保持干净的状态（commit 或接着 stash）。

Git自动给 `dev` 分支做了一次提交，注意这次提交的commit是 `1d4b803` ，它并不同于 `master` 的`869da06`，因为这两个commit只是改动相同，但确实是两个不同的commit。用 `git cherry-pick`，我们就不需要在 `dev` 分支上手动再把修bug的过程重复一遍（如果发生冲突，正常修改即可）。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228231131.png)

有些聪明的童鞋会想了，既然可以在 `master` 分支上修复bug后，在 `dev` 分支上可以“重放”这个修复过程，那么直接在 `dev` 分支上修复bug，然后在 `master` 分支上“重放”行不行？当然可以，不过你仍然需要 `git stash` 命令保存现场，才能从 `dev` 分支切换到 `master` 分支。

### Feature分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个 feature 分支，在上面开发，完成后，合并，最后，删除该 feature 分支。

现在，你终于接到了一个新任务：开发代号为 Vulcan 的新功能，该功能计划用于下一代星际飞船。

于是准备开发：

```shell
$ git switch -c feature-vulcan
Switched to a new branch 'feature-vulcan'
```

5分钟后，开发完毕：

```shell
$ git add vulcan.py

$ git status
On branch feature-vulcan
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   vulcan.py

$ git commit -m "add feature vulcan"
[feature-vulcan 287773e] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
```

切回 `dev`，准备合并：

```plain
$ git switch dev
```

一切顺利的话，feature 分支和 bug 分支是类似的，合并，然后删除。

但是！

就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

```plain
$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

销毁失败。Git友情提醒，`feature-vulcan` 分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。。

现在我们强行删除：

```plain
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
```

终于删除成功！