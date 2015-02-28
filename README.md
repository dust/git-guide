Git简易指南
===========
2015.2.13

# 配置git帐户
略。

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

## git add
* tracked
用个人理解的通俗说法，就是确认本次对某文件的修订。
* untrack
将某文件添加到版本控制系统。

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
将修订提到版本管理系统。
* commit-id/change-id

## 概念和流程
![流程及状态图](https://raw.githubusercontent.com/dust/git-guide/master/etc/status-flow.png)

* 理解暂存区(stage area)
先看看git add命令的官方解释。

    NAME
           git-add - Add file contents to the index
    DESCRIPTION
           This command updates the index using the current content found in the working tree, to prepare the content staged for the next commit. It typically
           adds the current content of existing paths as a whole, but with some options it can also be used to add content with only part of the changes made
           to the working tree files applied, or remove paths that do not exist in the working tree anymore.

           The "index" holds a snapshot of the content of the working tree, and it is this snapshot that is taken as the contents of the next commit. Thus
           after making any changes to the working directory, and before running the commit command, you must use the add command to add any new or modified
           files to the index.

           This command can be performed multiple times before a commit. It only adds the content of the specified file(s) at the time the add command is run;
           if you want subsequent changes included in the next commit, then you must run git add again to add the new content to the index.

           The git status command can be used to obtain a summary of which files have changes that are staged for the next commit.

git仓库保存着文件的所有历史版本及其相关索引。“这个命令将用在工作目录树上发现的当前内容去更新索引，并且为下次提交准备暂存内容。这个”索引“保持着一个指向工作目录树内容的快照。并且这个快照是为下次提交准备的内容。
           
* unmodified/modified/staged/<commit>
刚克隆、pull完成或提交后/修改并保存/git add后/提交(是一个动作，而不是一个持续的状态)


* git diff/git diff --staged(同--cached)
    git diff 比较的是当前文件内容（modified)和(最近一次)暂存区（add后)的文件变化。**而非当前文件内容(modified)和已提交(commit后)的变化**
    git diff --staged 比较的是当前staged（已暂存/add后）和(最近一次)提交(commit后)的快照的变化。
    每次提交(commit)的都是已暂存（staged）的快照，未包含到已暂存快照的文件(即未git add 进来的变化(modified))仍保持modified状态。每次提交都是对项目的一个快照，以后可以回到某个快照的状态，或者进行比较。(言外之意：**暂存区的状态是不可以回朔的**)。换个通俗的说法，就是每个git add都是更细粒度的对git commit的精确控制和准备。

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

另外，还有按照时间作限制的选项，比如--since(--after)和--until(--before)。下面的命令列出所有最近两周内的提交。

    git log --since=2.weeks

也可以给出各种时间格式，比如具体的某一天("2008-01-01")，或者多久时间以前("2 years 1 day 3 minutes ago")。还有按--author指定提交者，--grep从提交说明中搜索关键字。**如果要都匹配，则需要使用--all-match选项**，否则只要满足某一个条件就会被列出。**author & commiter**

## 撤销操作
撤销刚才所做的某些操作。有些撤销操作是不可逆的，所以请务必谨慎小心。

* 修改最后一次提交
适用于提交完后发现有文件没有加／删除，或者提交信息写错了，或者要撤回刚才的提交操作，可以使用--amend选项**重新**提交。

    git commit --amend

此命令将使用当前暂存区的快速提交。如果刚才的提交没有作任何改动，直接运行此命令，相当于有机会重新编写提交说明，将要提交的文件快照和之前的一样。

    git commit -m 'some plain text'
    git add forgotten_file
    git commit --amend

上述三条命令最终只产生一个提交，第二个提交命令修正了之前的一个提交（包括内容和提交说明文字）。

* 取消已经暂存的文件
如果某文件被不小心加到了暂存区，可以使用git reset HEAD <file>... 的方式取消暂存。执行此命令，<file>将回到**add之前的状态**。

* 取消对文件的修改
如果想要放弃对某个文件的修改，类似于cvs(clean copy),svn(revert)，可以使用git checkout -- <file>，这条命令有些危险，所有对此文件的修改及其状态都将丢失。如果需要同时保留修改以便将来使用，则可用stashing和分支来处理，会更健壮一些。


# 远程仓库的使用
## 当前远程仓库

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git remote --verbose
    origin	git@github.com:dust/git-guide.git (fetch)
    origin	git@github.com:dust/git-guide.git (push)

注意，如果有多个仓库此命令将全部将出，git remote --verbose，上例中只有origin是ssh url，也只有这个仓库，我才能push数据上去。

## 添加远程仓库
要添加远程仓库，可以指定一个简单的名字，以便将来引用，运行git remote add [shortname] [url]

    $ git remote
    origin
    $ git remote add pb git://github.com/paulboone/ticgit.git
    $ git remote -v
    origin git://github.com/schacon/ticgit.git
    pb git://github.com/paulboone/ticgit.git

现在可以用字符串 pb 指代对应的仓库地址了。比如说，要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 git fetch pb：
    
    $ git fetch pb
    remote: Counting objects: 58, done.
    remote: Compressing objects: 100% (41/41), done.
    remote: Total 44 (delta 24), reused 1 (delta 0)
    Unpacking objects: 100% (44/44), done.
    From git://github.com/paulboone/ticgit
    * [new branch] master -> pb/master
    * [new branch] ticgit -> pb/ticgit

现在，Paul 的主干分支（master）已经完全可以在本地访问了，对应的名字是 pb/master，你可以将它合并到自己的某个分支，或者切换到这个分支，看看有些什么有趣的更新。

## 从远程仓库抓取数据
可以使用git fetch [remote-name]从远程仓库中抓取数据到本地。此命令会抓取所有你本地仓库中还没有的数据。运行完成后，就可以在本地访问该远程仓库中中的所有分支，将其中某个分枝合并到本地，或者只是取出某个分枝。

如果是克隆了一个仓库，此命令会自动将远程仓库归于 origin 名下。所以，git fetch origin 会抓取从你上次克隆以来别人上传到此远程仓库中的所有更新（或是上次 fetch 以来别人提交的更新）。有一点很重要，需要记住，**fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支**，只有当你确实准备好了，才能手工合并。

如果设置了某个分支用于跟踪某个远端仓库的分支，可以使用 git pull 命令自动抓取数据下来，然后将远端分支自动合并到本地仓库中当前分支。在日常工作中我们经常这么用，既快且好。实际上，默认情况下 git clone 命令本质上就是自动创建了本地的 master 分支用于跟踪远程仓库中的 master 分支（假设远程仓库确实有 master 分支）。所以一般我们运行 git pull，目的都是要从原始克隆的远端仓库中抓取数据后，合并到工作目录中的当前分支。

## 推送数据到远程仓库
git push [remote-name] [branch-name]。如果要把本地的 master 分支推送到 origin 服务器上（克隆操作会自动使用默认的 master 和 origin 名字），可以运行下面的命令：

    $ git push origin master

只有在远程服务器上有写权限，而且同一时刻没有其他人在推数据，这条命令才会如期完成任务。如果在你推数据前，已经有其他人推送了若干更新，那你的推送操作就会被驳回。你必须先把他们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。

## 查看远程仓库信息
通过命令 git remote show [remote-name] 查看某个远程仓库的详细信息，比如要看所克隆的 origin 仓库，可以运行：

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git remote show origin
    * remote origin
      Fetch URL: git@github.com:dust/git-guide.git
      Push  URL: git@github.com:dust/git-guide.git
      HEAD branch: master
      Remote branch:
        master tracked
      Local branch configured for 'git pull':
        master merges with remote master
      Local ref configured for 'git push':
        master pushes to master (up to date)


除了对应的克隆地址外，它还给出了许多额外的信息。它友善地告诉你如果是在 master 分支，就可以用 git pull 命令抓取数据合并到本地。另外还列出了所有处于跟踪状态中的远端分支。

    $ git remote show origin
    * remote origin
    URL: git@github.com:defunkt/github.git
    Remote branch merged with 'git pull' while on branch issues
    issues
    Remote branch merged with 'git pull' while on branch master
    master
    New remote branches (next fetch will store in remotes/origin)
    caching
    Stale tracking branches (use 'git remote prune')
    libwalker
    walker2
    Tracked remote branches
    acl
    apiv2
    dashboard2
    issues
    master
    postgres
    Local branch pushed with 'git push'
    master:master

它告诉我们，运行 git push 时缺省推送的分支是什么（最后两行）。它还显示了有哪些远端分支还没有同步到本地（译注：第六行的 caching 分支），哪些已同步到本地的远端分支在远端服务器上已被删除（Stale tracking branches 下面的两个分支），以及运行 git pull 时将自动合并哪些分支（前四行中列出的 issues 和 master 分支）。

## 远程仓库的删除和重命名
git remote rename 命令修改某个远程仓库在本地的简称，比如想把 pb 改成 paul，可以这么运行：

    $ git remote rename pb paul
    $ git remote
    origin
    paul

注意，**对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 pb/master 分支现在成了 paul/master**。

远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 git remote rm 命令：

    $ git remote rm paul
    $ git remote
    origin

# 打标签(记)
## 列出现有标签

    $ git tag
    v0.1
    v1.3
    $ git tag -l 'v1.4.2.*'
    v1.4.2.1
    v1.4.2.2
    v1.4.2.3
    v1.4.2.4

## 新建标签
Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

* 含附注的标签

创建一个含附注类型的标签非常简单，用 -a （取 annotated 的首字母）指定标签名字即可：
``` shell
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git tag -a v2015.2.26.13.00 -m 'before'
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git tag
    v2015.2.26.13.00
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git show v2015.2.26.13.00
    tag v2015.2.26.13.00
    Tagger: dust <dust@downjoy.com>
    Date:   Thu Feb 26 15:28:12 2015 +0800
    
    before
    
    commit 23ff0573348364de0c0a67d27cabfb8f2bb95ffd
    Author: dust <dust@downjoy.com>
    Date:   Thu Feb 26 10:25:42 2015 +0800
    
    v2015.2.26.11.25
    
    diff --git a/README.md b/README.md
    index cc18db5..ffeb5fc 100644
    --- a/README.md
    +++ b/README.md
    @@ -64,4 +64,139 @@ Git简易指南
     ## git commit
     * 暂存区(stage area)
     * commit-id/change-id
     ....
```

* 含附注的标签

创建一个含附注类型的标签非常简单，用 -a （译注：取 annotated 的首字母）指定标签名字即可

    $ git tag -s v1.5 -m 'my signed 1.5 tag'
    You need a passphrase to unlock the secret key for
    user: "Scott Chacon <schacon@gee-mail.com>"
    1024-bit DSA key, ID F721C45A, created 2009-02-09

* 轻量级标签

轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。要创建这样的标签，一个 -a，-s 或 -m 选项都不用，直接给出标签名字即可

    $ git tag v1.4-lw
    $ git tag
    v0.1
    v1.3
    v1.4
    v1.4-lw
    v1.5

* 分享标签
默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行 git push origin [tagname] 即可。
``` shell
    $ git push origin v1.5
    Counting objects: 50, done.
    Compressing objects: 100% (38/38), done.
    Writing objects: 100% (44/44), 4.56 KiB, done.
    Total 44 (delta 18), reused 8 (delta 1)
    To git@github.com:schacon/simplegit.git
    * [new tag] v1.5 -> v1.5
```

# 分支
Git 的分支可谓是难以置信的轻量级，它的新建操作几乎可以在瞬间完成，并且在不同分支间切换起来也差不多一样快。和许多其他版本控制系统不同，Git 鼓励在工作流程中频繁使用分支与合并，哪怕一天之内进行许多次都没有关系。理解分支的概念并熟练运用后，你才会意识到为什么 Git 是一个如此强大而独特的工具，并从此真正改变你的开发方式。

## 理解分支

* 分支的存储结构

    $ git add README test.rb LICENSE
    $ git commit -m 'initial commit of my project'

这样的一个提交中，概念上来说，仓库中各个对象保存的数据和相互关系看起来如图所示：

![storage struct of a commit](https://raw.githubusercontent.com/dust/git-guide/master/etc/a-commit-struct.png)

仓库中共有5个对象，三个文件的快照内容blob对象，一个目录树索引对象，一个指向目录树(root)的索引及其它元数据的commit对象。如果再经过两次修改及提交后，仓库历史会变成如图所示的结构：

![多次提交后存储结构](https://raw.githubusercontent.com/dust/git-guide/master/etc/multi-commit-relationship-graph.png)

首次提次之后的每个提交对象还会包含一个指向上次提交对象的指针（parent).

Git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。Git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。

![master其实是一个缺省分支名](https://raw.githubusercontent.com/dust/git-guide/master/etc/master-as-default-branch.png)

创建一个新的分支指针。比如新建一个 testing 分支，可以使用 git branch 命令：

    $ git branch testing

这会在当前 commit 对象上新建一个分支指针，见下图。

![创建分支](https://raw.githubusercontent.com/dust/git-guide/master/etc/create-branch.png)

此时，其实有多个分支指向同一个提交，那么我当前工作在哪个分支上以及如何改变我当前工作的分支呢？ 其实着一个名为 HEAD 的特别指针，它是一个指向你正在工作中的本地分支的指针（可以将 HEAD 想象为当前分支的别名。）。运行 git branch 命令，仅仅是建立了一个新的分支，但不会自动切换到这个分支中去，所以在这个例子中，我们依然还在 master 分支里工作，见下图(HEAD 指向当前所在的分支)。

![多分支的结构（包含当前工作所在分支）](https://raw.githubusercontent.com/dust/git-guide/master/etc/multi-branch-struct.png)

要切换到其他分支，可以执行 git checkout 命令。我们现在转换到新建的 testing 分支：

    $ git checkout testing
    
这样 HEAD 就指向了 testing 分支。

![多分支工作环境](https://raw.githubusercontent.com/dust/git-guide/master/etc/multi-branch-work-environment.png)

如果此时，我们再提交一次。

    $ vim test.rb
    $ git commit -a -m 'made a change'

提交后的结果如图，并且每次提交后，HEAD 随着分支一起向前移动，而 master 分支仍然指向原先 git checkout 时所在的 commit 对象。

![在新分支上工作](https://raw.githubusercontent.com/dust/git-guide/master/etc/work-on-new-branch.png)

如果我们切换回到master分支

    $ git checkout master

![切换到master分支](https://raw.githubusercontent.com/dust/git-guide/master/etc/change-to-master-branch.png)

这条命令做了两件事。它把 HEAD 指针移回到 master 分支，**并把工作目录中的文件换成了 master 分支所指向的快照内容**。也就是说，现在开始所做的改动，将始于本项目中一个较老的版本。它的主要作用是将 testing 分支里作出的修改暂时取消，这样你就可以向另一个方向进行开发。

们作些修改后再次提交：

    $ vim test.rb
    $ git commit -a -m 'made other changes'

现在我们的项目提交历史产生了分叉，因为刚才我们创建了一个分支，转换到其中进行了一些工作，然后又回到原来的主分支进行了另外一些工作。这些改变分别孤立在不同的分支里：我们可以在不同分支里反复切换，并在时机成熟时把它们合并到一起。而所有这些工作，仅仅需要 branch 和 checkout 这两条命令就可以完成。

![在多分支上工作](https://raw.githubusercontent.com/dust/git-guide/master/etc/work-on-multi-branch.png)

Git 中的分支实际上仅是一个包含所指对象校验和（40 个字符长度 SHA-1 字串）的文件，所以创建和销毁一个分支就变得非常廉价。说白了，新建一个分支就是向一个文件写入 41 个字节（外加一个换行符）那么简单，当然也就很快了。Git 的实现与项目复杂度无关，它永远可以在几毫秒的时间内完成分支的创建和切换。同时，因为每次提交时都记录了祖先信息（译注：即 parent 对象），将来要合并分支时，寻找恰当的合并基础（即共同祖先）的工作其实已经自然而然地摆在那里了，所以实现起来非常容易。Git 鼓励开发者频繁使用分支，正是因为有着这些特性作保障。

## 分支的基本操作

* 创建及切换到分支

```
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git branch iss53
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout iss53
    Switched to branch 'iss53'
```

或者：

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout -b iss53
    Switched to branch 'iss53'

在分支iss53上工作：

    dust@dust-t400:~/myworks/kmfrog/git-guide$ vim test.py
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git add -u
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git commit -m 'change for [issue 53]'
    [iss53 949847f] change for [issue 53]
     1 file changed, 2 insertions(+)

现在仓库中如图：

![在iss53上产生一个提交](https://raw.githubusercontent.com/dust/git-guide/master/etc/branch-1.png)

如果现在需要马上修补master分支，有了 Git ，就可以不同时发布这个补丁和 iss53 里作出的修改，也不需要在master分支上复原iss53上的修改。唯一需要的仅仅是切换回 master 分支。

不过在此之前，留心你的暂存区或者工作目录里，那些还没有提交的修改，它会和你即将检出的分支产生冲突从而阻止 Git 为你切换分支。切换分支的时候最好保持一个清洁的工作区域。（扩展学习：有绕过这种问题的办法（分别叫做 stashing 和 commit amending））。目前已经提交了所有的修改，所以接下来可以正常转换到 master 分支：

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout master
    Switched to branch 'master'
    Your branch is up-to-date with 'origin/master'.
    
此时工作目录中的内容和你在解决问题 #53 之前一模一样。（**Git 会把工作目录的内容恢复为检出某分支时它所指向的那个提交对象的快照。它会自动添加、删除和修改文件以确保目录的内容和你当时提交时完全一样。**), 修订错误并测试。比如同样修改test.py文件。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout -b 'hotfix'
    Switched to a new branch 'hotfix'
    dust@dust-t400:~/myworks/kmfrog/git-guide$ vim test.py
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git add -u
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git commit -m 'fixed broken...'
    [hotfix 6390d0d] fixed broken...
     1 file changed, 3 insertions(+)

此时仓库中如图：

![在hotfix上产生一个提交](https://raw.githubusercontent.com/dust/git-guide/master/etc/branch-2.png)

经过验证后，确认修补成功。此时需要回到｀master `分支，把它合并进来，然后发布到产品环境。此时可以使用git merge命令进行合并。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout master
    Switched to branch 'master'
    Your branch is up-to-date with 'origin/master'.
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git merge hotfix
    Updating 75f605b..6390d0d
    Fast-forward
     test.py | 3 +++
     1 file changed, 3 insertions(+)
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git branch
      hotfix
      iss53
    * master

请注意，合并时出现了“Fast forward”的提示。由于当前 master 分支所在的提交对象是要并入的 hotfix 分支的直接上游，Git 只需把 master 分支指针直接右移。换句话说，如果顺着一个分支走下去可以到达另一个分支的话，那么 Git 在合并两者时，只会简单地把指针右移，因为这种单线的历史分支不存在任何需要解决的分歧，所以这种合并过程可以称为快进（Fast forward）。

现在最新的修改已经在当前 master 分支所指向的提交对象中了，可以部署到生产服务器上去了。

![master合并hotfix之后](https://raw.githubusercontent.com/dust/git-guide/master/etc/branch-3.png)

由于当前 hotfix 分支和 master 都指向相同的提交对象，所以 hotfix 已经完成了历史使命，可以删掉了。使用 git branch 的 -d 选项执行删除，然后再回到之前未完成的`iss53`分支上继续工作, 可以发现test.py的内容回到iss53最后一次提交的状态，而且不包含hotfix的内容。**也就是说iss53分支完全未受hotfix影响**。
     
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git branch -d hotfix
    Deleted branch hotfix (was 6390d0d).
    dust@dust-t400:~/myworks/kmfrog/git-guide$ vim test.py 
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git add -u
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git commit -m 'change2 for iss53'
    [iss53 fcc1a90] change2 for iss53
     1 file changed, 3 insertions(+)
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git branch
    * iss53
      master

* 合并分支

在iss53的工作完成之后，可以合并回 master 分支。

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git checkout master
    Already on 'master'
    Your branch is ahead of 'origin/master' by 2 commits.
      (use "git push" to publish your local commits)
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git merge iss53
    Auto-merging test.py
    CONFLICT (content): Merge conflict in test.py
    Automatic merge failed; fix conflicts and then commit the result.

因为master(hotfix)和iss53都修改了同一个文件的相同位置，所以合并出现冲突，手工处理完冲突文件后，使用git status确认。确认所有冲突都已解决，也就是进入了暂存区，就可以用 git commit 来完成这次合并提交。必要时给更多冲突处理细节，放到提交的注释之中(不采用-m)这种简易的方式提交。

```
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git status
    On branch master
    Your branch is ahead of 'origin/master' by 2 commits.
      (use "git push" to publish your local commits)

    All conflicts fixed but you are still merging.
      (use "git commit" to conclude merge)

    Changes to be committed:

	    modified:   test.py

    dust@dust-t400:~/myworks/kmfrog/git-guide$ git commit -m 'fixed conflict'
    [master bf9f2b6] fixed conflict
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git log --graph
    *   commit bf9f2b68e026cd8405dca05c947b4cde2613bdef
    |\  Merge: 7af0f18 fcc1a90
    | | Author: dust <dust@downjoy.com>
    | | Date:   Fri Feb 27 18:09:24 2015 +0800
    | | 
    | |     fixed conflict
    | |   
    | * commit fcc1a907e83e0c7260cda33af2a5e6aecc6ea000
    | | Author: dust <dust@downjoy.com>
    | | Date:   Fri Feb 27 17:57:47 2015 +0800
    | | 
    | |     change2 for iss53
    | |   
    | * commit 949847fac57e105b29c4471c502518f723b82edb
    | | Author: dust <dust@downjoy.com>
    | | Date:   Fri Feb 27 17:29:12 2015 +0800
    | | 
    | |     change for [issue 53]
    | |   
    * | commit 7af0f18fb8becffe274c1e2dc5d8d37b1a2f975d
    | | Author: dust <dust@downjoy.com>
    | | Date:   Fri Feb 27 18:04:24 2015 +0800
    | | 
    | |     addation commit
    | |   
    * | commit 6390d0d77579aff04d9647b594eca7565635adff
    |/  Author: dust <dust@downjoy.com>
    |   Date:   Fri Feb 27 17:44:43 2015 +0800
    |   
    |       fixed broken...
    |    
    *   commit 75f605b55794bada6e721dc7fd3b209807581a32
    |\  Merge: 496e3d3 6d24cc0
    | | Author: dust <dust@downjoy.com>
    | | Date:   Fri Feb 27 17:22:36 2015 +0800
    | | 
    | |     Merge branch 'master' of github.com:dust/git-guide
    | |     
    | *   commit 6d24cc074ba6c9ac2dd7b2805cf0cf076da95a91
    | |\  Merge: f3d7eee e5782c4
    | | | Author: dust <dust@kmfrog.com>
    | | | Date:   Fri Feb 27 17:09:39 2015 +0800
```

最终仓库如图：

![master合并hotfix之后](https://raw.githubusercontent.com/dust/git-guide/master/etc/branch-4.png)


