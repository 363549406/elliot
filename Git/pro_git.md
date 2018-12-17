# Pro Git


### 1.1 About Version Control

* Local Version Control Systems: 本地
* Centralized Version Control Systems: 有一个中心vcs server，各台pc只会获取最新版本代码
* Distributed Version Control Systems: 每个pc上都会有完整的version database

所以如果中心vcs server丢失就没有了version database，而分布式vcs中每台pc都可以恢复version database
不同于本地vcs和中心vcs中（如SVN）中存储的是changes (diff)，re-create的时候需要adding up all the patches。而分布式vcs中使用的是snapshot（快照），这使得git特别快。


### 1.3 Getting Started - Git Basics

Snapshots, not differences

commit的时候，git take a picture of 所有文件并store a reference to that snapshot. 为了提高效率，如果文件没有改变，Git不会再存储一遍那个文件，而是会link to先前已经存储过的文件。

Git中有working directory - Staging Area - .git directory (Repository) 三个States。

working directory (stage fixes ->) Staging Area (commit ->) .git directory

working directory (<- checkout the project) .git directory


### 1.6 Getting Staarted - First-Time Git Setup


* `/etc/gitconfig` - git config --system - all users
* `~/.gitconfig` or `~/.config/git/config` - git config --global - the user
* `repository/.git/config` - git config --local or git config - single repository

首先要设置user.name和user.email因为每次commit都会用到这两个信息


### 1.7 Getting Staarted - Getting Help

查看man
```
$ git help <verb>
$ man git-<verb>
```

参看参数
```
$ git <verb> -h
```

IRC channel - irc.freenode.net `#git` and `#github`


### 2.1 Git Basics - Getting a Git Repository

`$ git init` 会创建名为.git的子目录，含有初始化的Git仓库中所有的必须文件，但是项目里的文件还没有被tracked

`$ git clone https://github.com/libgit2/libgit2`
会把project中所有history version的数据都拉取下来。Git有不同的transfer protocol，如https、git:// (ssh)


### 2.2 Git Basics - Recordig Changes to the Repository

working directory中的文件可以有tracked和untracked两种状态。

untracked           unmodified           modified           staged
   |--add the file to the next commit------------------------->|
   |                     |--edit file------>|
   |                     |                  |--stage file----->|
   |<---remove file------|                  
   |                     |<----------------commit--------------|

.gitignore文件中设置不想tracked的文件，.gitignore的格式规范

1. `#` 被git忽略
2. 标准的glob模式匹配 如 *.pyc
3. 以(/)开头防止递归  /TODO 值忽略当前目录下的TODO文件，不会subdir/TODO
4. 以(/)结尾指定目录  build/
5. 要忽略指定模式以外的文件或目录，可以在模式前加(!)取反  如果.gitignore已经有*.a 再有 !lib.a 还是会track lib.a的。

`$ git diff` 会查看working directory和staged的差别，`--staged`或`--cached`查看staged和上次commit之间的差别，是要提交的信息。
`$ git difftool` 命令用其他输出方式如vimdiff来输出diff分析结果

`$ git commit` 提交 `-m`选项将提交信息和命令放在一起。`-a`选项可以把所有已经tracked的文件暂存起来一并提交。

从git中移除文件，需要从tracked files中移除文件并且commit。rm只是从working space中删除，git rm才是从tracked file中也删除。`$ git rm --cached README` 是从staging area中移除但是keep it in your working space

`$ git mv file_from file_to` 移动文件。再用`$ git status`查看的时候会提示renamed信息


### 2.3 Git Basics - Viewing the Commit History

`$ git log` 查看history信息。`-p or --patch`参数show the difference (the patch output) introduced in each commit, -n指定limit有几个commit会显示。--stat参数显示each commit entry a list of modifed files. --pretty参数改变format，有用的--pretty=oneline/short/full/fuller，--pretty=format:"%h - %an, %ar : %s"可以自定义格式。--graph参数add a nice little ASCII graph showing your branch and merge history.

Limiting Log Output

1. `--since and --until`  `$ git log --since=2.weeks` 日期格式很多 "2018-01-15" or "2 years 1 day 3 minutes ago"
2. `--author` filter on a specific author
3. `--grep` search for keywords in the commit messages
4. `-S` which takes a string and shows only those commits that changed the number of occurrences of that string
5. specify a directory or file name, limit the log output to a commits that introduced a change to those files. (also preceded by double dashes -- to sperate the path from the options.)
6. `--no-merges`  使用的workflow可能会导致log中有很多merge的commit，此参数，去除那类信息。


### 2.4 Git Basics - Undoing Things

`$ git commit --amend`   给个机会重写一次上次的commit message
`$ git reset HEAD <file>...`  to unstage - Unstaging a Staged File
`$ git checkout -- <file>...` to discard changes in working directory - Unmodifying a Modified File 注意这个命令很危险，你的改动checkout --后就再也找不到了。


### 2.5 Git Basics - Working with Remotes

`$ git remote` 查看所有remote -v shows URLs
`$ git remote add <shortname> <url>` 添加一个remote
`$ git fetch <remote>` 会pull down all the data from that remote project 好比新的分支。这是download下来，不会自动merge
`$ git pull <remote> <branch>` 如果branch已经set up to track a remote branch那直接git pull就可以了
`$ git push <remote> <branch>` push it upstream。push需要有权限。如果别人在你上次pull之后修改过remote内容，还需要先pull再push
`$ git remote show <remote>` 查看remote详细信息
`$ git remote rename pb pual` 给<remote>改名字
`$ git remote remove <remote>` remove <remote>


### 2.6 Git Basics - Tagging

通常用tag来mark release points

`$ git tag` 按照alphabetical顺序显示所有tag。使用-l或者--list参数来使用wildcards，如`git tag -l "v1.8.5*"`

tag的创建支持两种类型：lightweight和annotated。lightweight是pointer to a specific commit。annotated是有自己完整的信息，如checksummed, tagger name, email, etc.

`$ git tag -a v1.4 -m "my version 1.4"` 使用-a参数创建annotated tag, -m tagging message
`$ git show v1.4` 查看tag信息
`$ git tag v1.4-1w` 不使用-a, -s, or -m参数
`$ git tag -a v1.2 9fceb02` 在某个commit上生成tag
`$ git push <remote> tag` 和<remote>分享tag。或者使用--tags push所有的tag
`$ git tag -d v1.4-1w` 删除本地的tag。$ git push <remote> :refs/tags/v1.4-1w 更新到remote端
`$ git checkout <tag>` 这时会在detached HEAD。或者直接从tag checkout一个分支 $ git checkout -b <branch> <tag>


### 2.7 Git Basics - Git Aliases

不一定会用到，但是可以知道。使用git config来设置别名

`$ git config --global alias.co checkout` 后就可以是使用 $ git co 代替 $ git checkout
`$ git config --global alias.last 'log -l HEAD' $ git last` 展示最后一条commit信息
`$ git config --global alias.visual '!gitk' $ git visual` 用 ! 符号使用非git命令


### 3.1 Git Branching - Branches in a Nutshell

Git处理分支的方式轻量，创建分支、在不同分支间切换在一瞬间完成。

创建一个commit的时候：

1. 为每个修改的文件创建blobs
2. one tree that lists the contents of the directory and specifies which file names are stored as which blobs
3. one commit with the pointer to that root tree and all the commit metadata

再创建一个commit的时候。next commit stores a pointer to the commit that came immediately before it.

分支只是指向commit的指针，默认分支是master，其实master也是一个普通的分支，只是在git init的时候会被默认创建，而且大家都不修改它

创建一个新的分支`$ git branch testing` 只是create a new pointer to the same commit you're currently on.

git是如何知道当前在哪个分支。It keeps a special pointer called `HEAD`, which is a pointer to the local branch you're currently on.

切换分支就是把`HEAD` point to the swithing branch.

再次在新的分支上提交代码后，`HEAD`, new branch 都随着commit向前移动了。这个时候再切换回原来的分支（如master分支），其实做了两件事情：1. `HEAD` 指向改变；2. reverted the files in your working directory back to the snapshot that masters points to.

这个时候再在master分支上有新的commit，project history has diverged. 使用`$ git log --oneline --decorate --graph --all`可以查看分支diverged和merged的情况。

在Git中，一个分支is actually a simple file that contains the character SHA-1 checksum of the commit it points to, branches are cheap to create and destory. (40 characters checksum and a newline (a characters) , totall 41 bytes)


### 3.2 Git Branching - Basic Branching and Merging

merge的时候遇到过fast-forward. 当要merge的分支指向的commit是当前分支指向commit的directly ahead，这时候merge就是向前移动pointer。

还有一类merge，在两个已经分叉的分支上merge。这个时候会用到两个分支当前的snapshot还有它们common ancestor 的snapshot。并生成一个新的snapshot。新的commit就有了两个parent.

还有一类merge是有conflict的，这需要解决conflict.


### 3.3 Git Branching - Branch Management

`$ git branch --merged` 和 `$ git branch --no-merged` 可以查看合并了的和没有合并的到当前分支的分支。`$ git branch --merged a_branch`可以查看合并到`a_branch`分支的分支 without chekout to that branch.


### 3.4 Git Branching - Branching Workflows

常用的有一个用于发布的master、一个开发的develop、很多个基于topic的分支。（我们现在就是主项目的master，你fork项目的master，你fork项目的各个topic分支。有的时候省去fork项目的master分支，直接从fork项目的topic分支合并到主项目的master分支）


### 3.5 Git Branching - Remote Branches

`$ git remote show [remote]` 查看remote的信息。

remote-tracking branches are references to the state of remote branches. `<remote>/<branch>` 的形式。

`$ git fetch origin` looks up which server "origin" is, fetches any data from it that you don't yet have, and updates your local database, moving your origin/master pointer to its new, more up-to-date position. 本地的master和origin/master是可以diverge的。

`$ git remote add` 可以添加一个新的remote。当需要share a branch时需要将分支push到远端。

fetch的时候并没有把在本地生成一个分支，只是有了一个不可改变的origin/serverfix pointer。如果需要本地分支可以 `$ git checkout -b serverfix origin/serverfix`

Tracking Branches: 以上从remote-tracking branch automatically create的分支叫做tracking branch，远端的分支叫upstream branch。两者之间直接有联系。如果在tracking branch上输入 `$ git pull` git会自动知道需要使用哪个远端分支。

`$ git checkout --track origin/serverfix` : 直接在本地生成一个serverfix，tracking远端serverfix
`$ git checkout -b sf origin/serverfix` : 在本地生成一个sf，tracking远端serverfix
`$ git branch -u origin/serverfix` : 修改本地当前分支的upstream。`-u` or `--set-upstream-to`
`$ git branch -vv` : 展示分支中更多的信息，包括tracking的分支，以及本地分支is ahead，behind or both

`$ git fetch` 会拉去remote的数据，但是不会merge。`$ git pull` 就相当于 `$ git fetch`后面跟着 `$ git merge` 命令。

删除remote中的分支。`$ git push origin --delete serverfix`。如果远端分支和本地分支都删除了，但是在`$ git remote show <remote>`的时候还是有remote branch信息，可以通过`$ git branch -d -r <remote>/<branch-name>`来删除。如果想批量删除可以使用`$ git remote prune <remote>`


### 3.6 Git Branching - Rebasing

With the rebase command, you can take all the changes that were committed on one branch and replay them on another one.

```
$ git checkout experiment
$ git rebase master
```

It works by going to the common ancestor of the two branches ( the one you're on and the one you're rebasing onto ( the master branch )), getting the diff introduced by each commit of the branch you're on, saving those diffs to temporary files, resetting the current branch to the sam ecommit as the branch you are rebasing onto, and finally applying each change in turn.

上面的命令然后可以回到master分支，`git merge experiment` 这时候是fast-forward merge.

rebase和merge都能合并代码，但是rebasing makes for a cleaner history. 虽然最开始两个分支是parallel的，但是rabase后能让其变为series。

很重要的一点：

> Do not rebase commits that exist outside your repository.

因为当你rebase的时候，you're abandoning existing commits and creating new ones that are similar but different.

所以你可以rebasing your branches before they are shared.

不要rebase已经shared过的commit。


### 4.1 Git on the Server - The Protocols

四种协议：Local, HTTP, Secure Shell(SSH) and Git. 

最常用的是HTTPS的Smart HTTP和SSH。

Local Protocol 在remote repository是在同一个host的别的路径下。clone项目可以
```
$ git clone file:///srv/git/project.git
```

The Git Protocol, a special daemon that comes packed with Git; it listens on a dedicated port(9418)。没有认证。


### 4.2 - 4.9 Git on the Server

可以自己搭建git，git也有自带的GitWeb页面。但是建议直接使用GitLab或者第三方如GitHub等。


### 5.1 Distributed Git - Distributed Workflows

workflow并不是一定的。可以选择一个或者mixin多个。

Centralized Workflow 就是集中的一个central shared repository大家都有push权限。push之前先merge最新的代码。

Intergration-Manager Workflow 每个developer有自己repository的写权限和其他人的读权限。还有一个offical的project。开发者写到自己的repository然后给offical项目提交pull request。GitHub或GitLab中常用的是这种。

Dictator and Lieutenants Workflow （司令官与副官工作流）, Linux kernel是使用这种方式。副官的各个集成管理者负责项目中的特定部分。副官合并了其他开发者的提交到自己的master分之后再向司令官提交所有的代码。


### 5.2 Distributed Git - Contributing to a Project

Git source code中`Documentation/SubmittingPatches`文件中有tips for creating commits.

1. `git diff --check` 检查whitespace errors.
2. try to make each commit a logically separate changeset. 尽量做到一个issue至少有一个commit
3. 注意commit message. 第一行小于50 character 空格 details，尽量用现在时

### 10.2 Git Internals - Git Objects


