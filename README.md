Git简易指南
===========
2015.2.13

# 配置git帐户

# getting started
* 从本地创建仓库

``` shell
    dust@dust-t400:~/myworks/kmfrog$ mkdir git-guide
    dust@dust-t400:~/myworks/kmfrog$ cd git-guide
    dust@dust-t400:~/myworks/kmfrog/git-guide$ vim README.md
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git init
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git add README.md
    Initialized empty Git repository in /home/dust/myworks/kmfrog/git-guide/.git/
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git remote add origin git@github.com:dust/git-guide.git
    dust@dust-t400:~/myworks/kmfrog/git-guild$ git push -u origin master
```

* 从远程克隆仓库

``` shell
    dust@dust-t400:~/myworks/kmfrog$ git clone git@github.com:dust/git-guide.git
```
	
* push存在的本地仓库到远程

``` shell
    git remote add origin git@github.com:dust/git-guide.git
    git push -u origin master
```


# 基本概念和版本控制流程

![场景及流程图](https://raw.githubusercontent.com/dust/git-guide/master/etc/status-flow.png)

## git add
* tracked
* untrack

## .gitignore
* 所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式最后跟反斜杠（/）说明要忽略的是目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

.gitignore文件示例

    # 此为注释 – 将被 Git 忽略
    # 忽略所有 .a 结尾的文件
    *.a
    # 但 lib.a 除外
    !lib.a
    # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
    /TODO
    # 忽略 build/ 目录下的所有文件
    build/
    # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
    doc/*.txt


## git commit
* 暂存区(stage area)
* commit-id/change-id
* unmodified/modified/staged-->提交后，修改前/修改并保存/提交后
* git diff/git diff --staged(同--cached)
    git diff 比较的是最近一次暂存（commit)的modified和当前medified的变化。**而非当前modified和staged(“已提交/commit”)的变化**
    git diff --staged 比较的是最近一次push的staged和当前提交(commit)staged的变化。
    每次提交(commit)都是在暂存区中staged状态的一个快照，未包含到此次提交(commit)中的文件(即未git add 进来的变化/modified)仍保持modified状态，以后可以回到某个快照的状态，或者进行比较。换个通俗的说法，就是每个git add都是更细粒度的对git commit的精确控制和准备。

## (从git)移除文件
使用git rm 完成移除操作，包括从git跟踪系统中移除连带从工作目录中删除文件。如果只是从工作目录中删除文件，在git status时，时就会显示在Changes not staged for commit之中，这里可以再次运行git rm完成从跟踪系统中移除此文件。

移除的文件不再出现在跟踪系统之中，但是它的快照版本仍存在于暂存区，如果要删除它的话，需要在命令中添加-f(--force)的选项。还有一种情况是希望从跟踪系统中删除，而在工作目录仍然保留，如编译的二进制文件，对象文件等。先在.gitignore中加入合适的选项，然后使用git rm --cached命令删除即可。
``` shell
git rm --cached readme.txt
```

删除文件时，可以列出文件或者目录的名字，也可以使用glob模式。如：
``` shell
git rm log/\*.log
git rm \*~
```
第一行中的＊之前的反斜杠\，因为Git有它自己的文件模式扩展匹配方式，所以我们不用shell来帮忙展开。（事实上不用反斜杠也可以运行，只不过按照shell扩展的话，仅仅删除指定目录下的文件，而不会递归匹配。因为这一行本来就指定了目录，所以效果是相同的。而第二行中则必须加上反斜杠，它会递归删除当前及其子目录中所有～结尾的文件。

## 移动文件（重构）
* git mv
    相当于下面三条命令。
``` shell
mv README.txt README
git rm README.txt
git add README
```
即便你分开进行上述三个操作，git也会意识到这是一次重命名操作。但如果手工或使用其它工程改名的话，注意在提交前先remove, 然后再add操作。

## 查看提交历史
在提交到若干更新后，或者克隆了某个项目，想回顾一下提交历史，可以使用git log命令查看。

    dust@dust-t400:~/myworks/kmfrog/git-guide$  git log
    commit 8e9ee7b3b25c363fd002e2a975de754af900e9c1
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:58:00 2015 +0800

        v2015.2.13.11.57

    commit 812441117293b2088fed0013f3f62fcd2be68a39
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:54:41 2015 +0800

        v2015.2.13.11.54

    commit 2c066b83a1d5c61326e88de1ac051e16b2c9b124
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:52:54 2015 +0800

        v2015.2.13.11.52

    commit 169f0524f1e33288f789e0d9ba104f5dfaeb4316
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:46:08 2015 +0800

        v2015.2.13.11.45
    :
    
按照提交时间列出所有更新，最近的更新排在最上面。sha-1, author, date以及一个缩进的提交说明。

有一些选项可以帮助你搜寻感兴趣的提交。常用-p选项展开每次提交的内容差异。用-2则仅显示最近两次更新。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git log -p -2
    commit 8e9ee7b3b25c363fd002e2a975de754af900e9c1
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:58:00 2015 +0800

        v2015.2.13.11.57

    diff --git a/README.md b/README.md
    index 36ade77..cc18db5 100644
    --- a/README.md
    +++ b/README.md
    @@ -34,7 +34,7 @@ Git简易指南
     
     # 基本概念和版本控制流程
     
    -[场景及流程图](!https://raw.githubusercontent.com/dust/git-guide/master/etc/sta
    +![场景及流程图](https://raw.githubusercontent.com/dust/git-guide/master/etc/sta
     
     ## git add
     * tracked
    @@ -47,6 +47,7 @@ Git简易指南
     * 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。
     
     .gitignore文件示例
    :

在做代码审查，或者要快速浏览其它协作者提交的更新都作了哪些改动时，可以使用这个选项。此外，还有许多摘要选项可以用。比如--stat，仅显示简要的增改行数统计。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git log --stat
    commit 8e9ee7b3b25c363fd002e2a975de754af900e9c1
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:58:00 2015 +0800

        v2015.2.13.11.57

     README.md | 5 ++++-
     1 file changed, 4 insertions(+), 1 deletion(-)

    commit 812441117293b2088fed0013f3f62fcd2be68a39
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:54:41 2015 +0800

        v2015.2.13.11.54

     README.md | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)

    commit 2c066b83a1d5c61326e88de1ac051e16b2c9b124
    Author: dust <dust@downjoy.com>
    Date:   Fri Feb 13 11:52:54 2015 +0800

        v2015.2.13.11.52
    :

其它选项[扩展学习](http://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html)中--pretty的online及format部分。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git log --pretty=format:"%h %cn %cd %p %s" --graph
    * 8e9ee7b dust Fri Feb 13 11:58:00 2015 +0800 8124411 v2015.2.13.11.57
    * 8124411 dust Fri Feb 13 11:54:41 2015 +0800 2c066b8 v2015.2.13.11.54
    * 2c066b8 dust Fri Feb 13 11:52:54 2015 +0800 169f052 v2015.2.13.11.52
    * 169f052 dust Fri Feb 13 11:46:08 2015 +0800 ec03e94 v2015.2.13.11.45
    * ec03e94 dust Fri Feb 13 11:43:47 2015 +0800 463aa10 v2015.2.13.11.43
    *   463aa10 dust Fri Feb 13 11:37:41 2015 +0800 3cabfb4 53a8932 Merge branch 'master' of github.com:dust/git-guide
    |\  
    | * 53a8932 dust Fri Feb 13 11:34:21 2015 +0800  Initial commit
    * 3cabfb4 dust Fri Feb 13 11:20:19 2015 +0800  readme v2015.2.13.11.20






