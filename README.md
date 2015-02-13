Git简易指南
===========
2015.2.13

# 配置git帐户

# getting started
* 从本地创建仓库  

    dust@dust-t400:~/myworks/kmfrog$ mkdir git-guide
    dust@dust-t400:~/myworks/kmfrog$ cd git-guide
    dust@dust-t400:~/myworks/kmfrog/git-guide$ vim README.md
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git init
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git add README.md
    Initialized empty Git repository in /home/dust/myworks/kmfrog/git-guide/.git/
    dust@dust-t400:~/myworks/kmfrog/git-guide$ git remote add origin git@github.com:dust/git-guide.git
    dust@dust-t400:~/myworks/kmfrog/git-guild$ git push -u origin master
    
* 从远程克隆仓库   

	dust@dust-t400:~/myworks/kmfrog$ git clone git@github.com:dust/git-guide.git
	
* push存在的本地仓库到远程   

    git remote add origin git@github.com:dust/git-guide.git
    git push -u origin master


# 基本概念和版本控制流程

[场景及流程图](!https://github.com/dust/git-guide/blob/master/etc/status-flow.png)

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

