---
title: git rebase常见的使用场景
date: 2019-11-12 16:11:28
tags: [git]
category: [git]
---

git rebase常见的使用场景

### git rebase 合并多次commit为一次

https://blog.csdn.net/itfootball/article/details/44154121

* 查看历史

```
$ git log
commit 415a0be986a48113829b3c60ee2387c6dbdc81d8
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 20:11:29 2015 +0800

    10->1

commit ed09a6cbe0797275ceead3f8c8d829d01f0e604b
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:57:17 2015 +0800

    2015.01.26

commit 1821f6a1c1ed074fe886228cf33b3b3cb71819c4
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:53:28 2015 +0800

    2015.01.26

commit cc779982fc61e82ec494d6a5654417fa7194d748
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:40:24 2015 +0800

    2015.1.26
```
    
我们看到上面有四次commit，如何将这个四次合并到一次呢？

* 压缩

```
git rebase -i HEAD~4
```

最后一个数字4代表压缩最后四次提交。
该命令执行后，会弹出vim的编辑窗口，4次提交的信息会倒序排列，最上面的是第四次提交，最下面的是最近一次提交

![](media/15687736182821/15687738542162.jpg)

我们需要修改第2-4行的第一个单词pick为squash，这个意义为将最后三次的提交压缩到倒数第四次的提交，效果就是我们在pick所在的提交就已经做了4次动作，但是看起来就是一次而已：
![](media/15687736182821/15687738746543.jpg)

保存退出，git会一个一个压缩提交历史，如果有冲突，需要修改，修改的时候要注意，保留最新的历史，不然我们的修改就丢弃了。修改以后要记得敲下面的命令：

```
git add .
git rebase --continue
```

如果想放弃这次压缩的话，执行以下命令：

```
git rebase --abort
```
所有冲突都已经解决了，会出现如下的编辑窗口
![](media/15687736182821/15687739470378.jpg)

这个时候我们需要修改一下合并后的commit的描述信息，我们将其描述为helloworld吧：
![](media/15687736182821/15687739644947.jpg)

保存退出后会看到我们完整的信息：

```
$ git rebase -i HEAD~4
[detached HEAD 9097684] helloworld
 Author: xuxu <xuxu_1988@126.com>
 Committer: unknown <hui.qian@HuiQianPC.spreadtrum.com>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 9 files changed, 25 insertions(+), 11 deletions(-)
Successfully rebased and updated refs/heads/master.

```

现在我们来查看一下历史：

```
$ git log
commit 90976848524251b0a62376a9e45ea5c8aae25d87
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:40:24 2015 +0800

    helloworld

commit f57da9460196b950036fe4c2c8f7e4c9131ee04e
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:38:27 2015 +0800

    2015.01.26

commit 64104e057f48d6dd0e432af8b55cbccd0a09ee63
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 19:31:42 2015 +0800

    2015.01.26

commit 8499d4fa23c4395660d21e0b7dca873a7a3ef2b8
Author: xuxu <xuxu_1988@126.com>
Date:   Mon Jan 26 18:58:40 2015 +0800

    2015.01.26
```

* 同步到远程仓库

```
git push
```
执行之后会被拒绝，因为我们合并的历史记录已经在远程仓库之前了，你无法覆盖它。那怎么办？如果你确定你的合并过程都是正确无误的，那么就可以强制push：

```
git push -f
```

ok!

* 遗留问题
1.这个方式的局限性是，我无法单独建立一个分支，然后在分支上做压缩动作，再合并到master上，在master上直接操作就存在很大的风险。也可能是我暂时还不会这么做吧，希望以后能找到答案。
2.直接强制的push到远程仓库也会存在问题，别人pull下来后是不是也会有问题。这个也需要探究
3.这种方式可以适用在本地分支之间的合并，你在dev分支中做了很多开发工作，修改历史很多，现在你开发完成了，你就要合并到master分支，然后提交到远程仓库。这个时候你可以用上面的流程先把dev的历史记录压缩一下，然后合并到master中。如果你是处在协同开发环境下，你还需要主要经常做远程仓库同步到master的动作，master的更新再rebase到dev分支。也需要探究


### git rebase 合并分支

https://xiaozhuanlan.com/topic/6873210549

dev-1分支
master分支
* 基于master分支检出dev-1分支
* dev-1分支提交多次，但不要push到远端

dev-1分支在提交的过程中有其他同事在master分支上提交了dev-2的更新，这个时候就需要将当前dev-1的变更变基到master

* 切换到dev-1分支
git checkout dev-1

* 将当前dev-1的变更变基到master
git rebase master

* 遇到冲突手动解决冲突，然后继续执行rebase
git rebase --continue

rebase之后dev-1已经是基于最新的master了

* 将dev-1推送至远端
* 切换到master分支拉取dev-1分支



