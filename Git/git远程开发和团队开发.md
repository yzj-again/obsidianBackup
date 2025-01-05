---
date: 2024-12-28
tags:
  - Git
---
Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。怎么分布呢？最早，肯定只有一台机器有一个原始版本库，此后，别的机器可以“克隆”这个原始版本库，而且每台机器的版本库其实都是一样的，并没有主次之分。

你肯定会想，至少需要两台机器才能玩远程库不是？但是我只有一台电脑，怎么玩？

其实一台电脑上也是可以克隆多个版本库的，只要不在同一个目录下。不过，现实生活中是不会有人这么傻的在一台电脑上搞几个远程库玩，因为一台电脑上搞几个远程库完全没有意义，而且硬盘挂了会导致所有库都挂掉，所以我也不告诉你在一台电脑上怎么克隆多个仓库。

实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

完全可以自己搭建一台运行Git的服务器，不过现阶段，为了学Git先搭个服务器绝对是小题大作。好在这个世界上有个叫GitHub的神奇的网站，从名字就可以看出，这个网站就是提供Git仓库托管服务的，所以，只要注册一个GitHub账号，就可以免费获得Git远程仓库。

下面是本地库和远程库的交互方式，有以下两种：

## 团队内部协作

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224202050.png)

### 初始化本地库和远程库

先由 A 同事初始化本地库操作不再演示，直接初始化远程库。

初始化远程库后，我们可以点击 `code`，打开后复制 https 或 ssh 进行连接。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241228234214.png)

每次提交我们都要输入很长的地址，于是我们可以给远程库地址起别名。提交本地库到远程时，第一次可以使用 `git push -u origin master`。当你第一次使用这个命令时，会将本地分支推送到远程仓库，并将本地 `master` 分支与远程 `origin/master` 分支建立跟踪关系。以后，你只需要执行 `git push`（不带任何参数）就可以将本地 `master` 分支的改动推送到远程 `origin/master` 分支。

```shell
$ git remote add origin https://github.com/yzj-again/testRepository.git
$ git remote -v
origin  https://github.com/yzj-again/testRepository.git (fetch)
origin  https://github.com/yzj-again/testRepository.git (push)
$ git push -u origin master
Enumerating objects: 25, done.
Counting objects: 100% (25/25), done.
Delta compression using up to 4 threads
Compressing objects: 100% (16/16), done.
Writing objects: 100% (25/25), 2.00 KiB | 684.00 KiB/s, done.
Total 25 (delta 7), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (7/7), done.
To https://github.com/yzj-again/testRepository.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.
```
推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样。

**删除远程库**

如果添加的时候地址写错了，或者就是想删除远程库，可以用 `git remote rm <name>` 命令。使用前，建议先用 `git remote -v` 查看远程库信息：

```shell
$ git remote -v
origin  https://github.com/yzj-again/testRepository.git (fetch)
origin  https://github.com/yzj-again/testRepository.git (push)
```

然后，根据名字删除，比如删除`origin`：

```shell
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

### 克隆

上次我们讲了先有本地库，后有远程库的时候，如何关联远程库。

现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，B 同事直接从远程库克隆。

```shell
$ git clone https://github.com/yzj-again/testRepository.git
Cloning into 'testRepository'...
remote: Enumerating objects: 25, done.
remote: Counting objects: 100% (25/25), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 25 (delta 7), reused 25 (delta 7), pack-reused 0 (from 0)
Receiving objects: 100% (25/25), done.
Resolving deltas: 100% (7/7), done.
$ cd testRepository 
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

> 默认是克隆的是master分支，想要克隆指定的分支，需要 -b 指定要克隆的分支`git clone <远程仓库的网址> -b <分支名称>`
> clone操作会做三件事：
> 1. 初始化本地库
> 2. 将远程库内容克隆到本地
> 3. 替我们创建化远程别名

### 团队操作

因为远程库是 A 创建的，B 同事能否有推送权力，是要由 A 决定的。所以需要团队操作权限。如果B直接推送，则会出问题。

![Snipaste_2024-12-27_23-57-49.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-27_23-57-49.png)

必须要加入团队才可以继续操作，A 登录 Gitlab 账号，邀请普通成员 B。

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241229003055.png)

![Snipaste20241227235830.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste20241227235830.png)

添加成员后，给这个成员发送邀请链接，被邀请人打开邀请链接，接受邀请。

![Snipaste_20241228_00-00-51.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_20241228_00-00-51.png)

此时已经完成了加入团队的操作，此时可以进行后续操作。

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```shell
$ git push origin master
```

如果要推送其他分支，比如 `dev`，就改成：

```shell
$ git push origin dev
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master` 分支是主分支，因此要时刻与远程同步；
- `dev` 分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug 分支只用于在本地修复 bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个 bug；
- feature 分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

### 拉取分支

多人协作时，大家都会往 `master` 和 `dev` 分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：

```shell
$ git clone git@github.com:michaelliao/learngit.git
Cloning into 'learngit'...
remote: Counting objects: 40, done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 40 (delta 14), reused 40 (delta 14), pack-reused 0
Receiving objects: 100% (40/40), done.
Resolving deltas: 100% (14/14), done.
```

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的 `master` 分支。不信可以用 `git branch` 命令看看：

```shell
$ git branch
* master
```

现在，你的小伙伴要在 `dev` 分支上开发，就必须创建远程 `origin` 的 `dev` 分支到本地，于是他用这个命令创建本地` dev` 分支：

```shell
$ git branch -vv
* master ab3e95e [origin/master] merged bug fix 101
# 获取最新的远程分支信息 
$ git fetch origin
$ git switch -c dev origin/dev
# 或 git pull origin <远程分支名>:<本地分支名>
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
Switched to a new branch 'dev'
$ git branch -vv
* dev    f8c748a [origin/dev] fix conflict
  master ab3e95e [origin/master] merged bug fix 101
```

现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程：

```plain
$ git add env.txt

$ git commit -m "add env"
[dev 7a5e5dd] add env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   f52c633..7a5e5dd  dev -> dev
```

> `git branch -vv` 这个命令会以详细的形式展示所有本地分支，包括每个分支是否跟踪远程分支，以及跟踪的远程分支是哪个。
> 
> ```shell
>   dev    f8c748a [origin/dev] fix conflict
> * master ab3e95e [origin/master] merged bug fix 101
> ```

当有人提交到远端后，此时其他人可以拉取代码到本地库。

`git pull` 操作等于 `fetch` 抓取和 `merge` 合并操作的合并。

![Snipaste_2024-12-28_00-06-13.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-06-13.png)

在抓取操作后，只是将远程库内容下载到本地，工作区中的内容并没有进行更新。我们可以先切换到 `origin/dev` 分支查看内容是否正确，在进行合并操作。

发现内容都正确，就可以切换到 `dev` 分支进行合并操作。

>`git pull origin master` = `git fetch origin master` + `git merge origin/master`

> `git pull` 如果失败了，原因可能是没有指定本地 `dev` 分支与远程 `origin/dev` 分支的链接，根据提示，设置 `dev` 和 `origin/dev` 的链接：
> ```shell
> $ git branch --set-upstream-to=origin/dev dev
> Branch 'dev' set up to track remote branch 'dev' from 'origin'.
> ```

### 团队开发合并冲突

B 已经向 `origin/dev` 分支推送了他的提交，而碰巧 A 对同样的文件作了修改，并试图推送：

```plain
$ cat env.txt
env

$ git add env.txt

$ git commit -m "add new env"
[dev 7bd91f1] add new env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
To github.com:michaelliao/learngit.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

推送失败，因为 B 的最新提交和 A 试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用 `git pull` 把最新的提交从 `origin/dev` 抓下来，然后，在本地合并，解决冲突，再推送。

![Snipaste_2024-12-28_00-20-26.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-20-26.png)

可以看到拉取时已经提醒有冲突了，和之前[解决冲突](git的基础知识#解决冲突)方式类似，然后再进行`add->commit->push`

## 跨团队协作

如果是公司外跨团队合作，按图下流程进行：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20241224202605.png)

在A公司内进行开发和之前团队内部协作开发流程类似。我们从B公司开始。

B公司先得到A公司的远程库地址，然后B 公司人员 C `fork` A 公司项目。

![Snipaste_2024-12-28_00-27-05.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-27-05.png)

接下来对 fork 的项目 clone 到本地，在本地进行开发，C 进行 push 推送到 B 公司远程库。

## pull request

在B公司仓库页面有一个 `Pull requests` 选项，点击进入后，再点击 `New pull request` 按钮。

![Snipaste_2024-12-28_00-30-22.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-30-22.png)

进入页面后，点击 `Create pull request`，在新页面输入当前合并请去信息，完成后点击 `Create pull request`。

![Snipaste_2024-12-28_00-31-13.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-31-13.png)


![Snipaste_2024-12-28_00-32-22.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-32-22.png)


![Snipaste_2024-12-28_00-32-56.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-32-56.png)

B 公司发起请求后，A 公司要审核合并请求。

![Snipaste_2024-12-28_00-33-38.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-33-38.png)

![Snipaste_2024-12-28_00-34-45.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-34-45.png)

![Snipaste_2024-12-28_00-35-22.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-35-22.png)

审核时，可以看 B 公司对项目做了哪些改动。查收代码无误后，可以进行合并操作。

![Snipaste_2024-12-28_00-37-30.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-37-30.png)

![Snipaste_2024-12-28_00-38-55.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/Snipaste_2024-12-28_00-38-55.png)

合并到 A 公司远程库后，A 公司其他人可以拉取或克隆远程库，查看合并内容，拉取时可能会发生冲突，冲突解决看前文提到的内容。