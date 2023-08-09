# Git

## Git概述

> Git是一个免费开源的分布式版本控制系统
>
> * 性能优于SVN（SubVersion）、CVS等版本控制工具
>   * 集中式版本控制工具SVN、CVS、VSS
>   * 分布式版本控制工具Git、Darcs
> * 版本控制最重要的就是记录文件修改的历史记录，从而让用户能够查看历史版本，方便版本切换
> * 为何需要版本控制工具：由个人开发过渡到团队协作
> * 集中式版本控制工具SVN介绍：
>   * 版本控制系统都有一个单一的集中管理的服务器
>   * 好处：
>     * 每个人都能在一定程度看到其他人正在做些什么
>     * 管理员轻松掌控每个开发者的权限
>     * 管理集中化的版本控制系统，远比在各个客户端维护本地数据库来的轻松
>   * 坏处：
>     * 中央服务器的单点故障；
>     * 集中管理的服务器宕机时间内，任何人无法完成提交更新的协同工作
> * 分布式版本控制工具Git介绍：
>   * 每个用户在自己的本地库即可对文件进行版本的控制
>   * 远程库：GitHub、Gitee
>   * 服务器断网情况下也可以进行开发（因为版本控制是在本地进行的）
>   * 每个客户端保存的也都是完整的项目（包含历史记录，更加安全）

> ==Git工作机制：==
>
> * 工作区（写代码）：代码在本地磁盘存储的位置
> * 暂存区（临时存储）：将工作区的代码添加到暂存区（git add）
> * 本地库（历史版本）：将暂存区的代码提交到本地库（git commit）
> * 远程库==代码托管中心：将本地库的代码推送到远程库（git push）

> ==Git和代码托管中心：==
>
> * 代码托管中心是基于网络服务器的远程代码仓库，一般简称为远程库。
> * 局域网的代码托管中心有GitLab
> * 互联网的代码托管中心有GitHub、Gitee





## Git安装

> 官网发布最新版本：2.31.1

> Git Bash Here：Git命令行客户端
>
> Git GUI Here：Git图形化客户端

> git --version：Git版本控制工具的版本



## Git常用命令

| 命令名称（Linux）                    | 功能作用                 |
| ------------------------------------ | ------------------------ |
| git config --global user.name 用户名 | 设置用户签名（一次）     |
| git config --global user.email 密码  | 设置用户签名（一次）     |
| git init                             | 初始化本地库             |
| git status                           | 查看本地库状态           |
| git add 文件名                       | 体检暂存区               |
| git commit -m "日志信息" 文件名      | 提交本地库               |
| git relog                            | 查看版本信息（历史记录） |
| git rest --hard 版本号               | 版本穿梭                 |

### ==设置用户签名：==

> * ```sh
>  git config --global user.name 用户名
>   git config --global user.email 邮箱
>   ```
> 
> * 注意：
>
>   * 作用是区分不同操作者身份
>  * 设置用户签名只是为了能够进行提交代码
>   * 此处设置的用户签名和将来登录GitHub（或其他版本托管中心）的账号没有任何关系
> 
> * 用户签名设置完成后，可以在家目录下看到.gitconfig的文件
>
>   * ```sh
>    ===家目录===
>     C:\Users\Administrator（此处可以通过Git Bash Here后的命令行最前部分确定）
> 
>     ===文件内容===
>           [user]
>     	name = Lixc
>     	email = vc572936348@yeah.net
>     [credential]
>     	helper = manager
>     ```

### ==初始化本地库：git init==

> * ```sh
>  git init
> 
>   ===执行完成后，会生成.git隐藏文件；===
>       ===在Linux命令中使用ll来查看当前目录下文件，但由于是隐藏文件需要使用ll -a来查看===
>   ```

### ==查看本地库状态：git status==

> * ```sh
>  git status
>   ===首次查看时，由于工作区（写代码）没有任何文件，会输出nothing to commit巴拉巴拉===
> 
>   ===新增文件===
>       vim hello.txt
>   ===按i进行新增数据；对内容进行复制粘贴：Esc后 yy复制，p粘贴===
>   hello git!---lixc
>   ===Esc后:wq保存文件===
> 
>   ===此时在查看本地库状态===
>       git status
>   ===就会检测到未追踪的文件Untracked files .... hello.txt（红色） ===
> 
>   ```

### ==添加暂存区：git add 文件名== 

> * ```sh
>   ===将工作区的文件添加到暂存区（git add）===
>   git add hello.txt
>   ===会有警告LF（Linux换行符）会被CRLF（Windows换行符）替换掉；无需关心===
>
>   ===此时再查看本地库状态===
>   git status
>   ===输出Changes to be committed .... hello.txt（绿色） ===
>
>   ===此时文件已经由工作区添加到暂存区了；可以删除：git rm --cached 文件名===
>   git rm --cached helo.txt
>   ===只是删除了暂存区的文件，工作区的文件还存在===
>   ```

### ==提交本地库==

> * ```sh
>   ===将暂存区的文件提交到本地库（git commit）-提交到本地库才会形成文件的历史版本===
>   === -m "日志信息"：需要定义版本日志信息；在git commit时即便不添加-m命令，也会自动弹出文件框定义日志信息===
>   git commit -m "my first commit" hellogit.txt
>   warning: in the working copy of 'hellogit.txt', LF will be replaced by CRLF the next time
>   Git touches it
>   ===这里的2fc1f95七位数字2fc1f95就是文件提交本地库后生成的版本号===
>   [master (root-commit) 2fc1f95] my first commit
>    1 file changed, 7 insertions(+)
>    create mode 100644 hellogit.txt
>   
>   ===再次执行git status查看本地库状态：nothing to commit 表明已经提交过了存在版本信息===
>   git status
>   On branch master
>   nothing to commit, working tree clean
>   ```

### ==修改文件==

> * ```sh
>   git status
>   On branch master
>   nothing to commit, working tree clean
>   ===修改文件===
>   vim hellogit.txt
>   ===此时查看本地库状态：文件又变为红色---添加暂存区才会变为绿色===
>   git status
>   On branch master
>   Changes not staged for commit:
>     (use "git add <file>..." to update what will be committed)
>     (use "git restore <file>..." to discard changes in working directory)
>           modified:   hellogit.txt
>   ===添加暂存区===
>   git add hellogit.txt
>   warning: in the working copy of 'hellogit.txt', LF will be replaced by CRLF the next time
>   Git touches it
>   ===查看本地库状态：此时文件变为绿色===
>   git status
>   On branch master
>   Changes to be committed:
>     (use "git restore --staged <file>..." to unstage)
>           modified:   hellogit.txt
>   ===对文件进行本地库提交操作===
>   git commit -m "version 3.1 commit" hellogit.txt
>   warning: in the working copy of 'hellogit.txt', LF will be replaced by CRLF the next time
>   Git touches it
>   [master ceec097] version 3.1 commit
>    1 file changed, 1 insertion(+)
>   ===再次查看本地库状态：没问题了===
>   git status
>   On branch master
>   nothing to commit, working tree clean
>   ```

### ==历史版本&&版本穿梭==

> * ```sh
>   ===查看版本详细信息（历史记录）===
>   git reflog
>   ceec097 (HEAD -> master) HEAD@{0}: commit: version 3.1 commit
>   653d2b0 HEAD@{1}: commit: my third commit
>   93838dc HEAD@{2}: commit: my second commit
>   2fc1f95 HEAD@{3}: commit (initial): my first commit
>   ===查看版本详细信息（历史记录）的命令：可以看到提交的版本号、操作者信息、提交日期、提交时定义的日志信息===
>   git log
>   commit 2fc1f955e7f6c8728468af4000c1f62001eafa27 (HEAD -> master)
>   Author: Lixc <vc572936348@yeah.net>
>   Date:   Tue Jul 4 15:31:49 2023 +0800
>   
>       my first commit
>   ===版本穿梭：git reset --hard 精简版本号 ===
>    git reset --hard 93838dc
>   HEAD is now at 93838dc my second commit
>   ===再次查看版本详细信息（历史记录）：会发现当前本地库指向了历史版本===
>   git reflog
>   93838dc (HEAD -> master) HEAD@{0}: reset: moving to 93838dc
>   ceec097 HEAD@{1}: commit: version 3.1 commit
>   653d2b0 HEAD@{2}: commit: my third commit
>   93838dc (HEAD -> master) HEAD@{3}: commit: my second commit
>   2fc1f95 HEAD@{4}: commit (initial): my first commit
>   ===查看文件：也变为了指定历史版本号时的文件===
>   cat hellogit.txt
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>        ---Lixc
>    version 2.0
>    
>    ===D:\PlantingTrees\java_install\3-Git\Git-Space\git-01demo\.git\refs\heads下面的master文件中记录了当前本地库管理的文件的版本号===
>    Git进行版本切换操作，底层其实就是移动的EAD指针，指针指向了master分支，master分支指向了版本号。
>   ```







## Git分支操作

> 版本控制过程中，同时推进多个任务，就可以为每个任务创建每个任务的单独分支。
>
> * 意味着程序员可以将自身工作从开发主线分离，开发自己的分支，不会影响主线分支运行。
> * 分支的底层也是指针的引用。
>
> 分支的好处：
>
> * 同时并行推进多个功能的开发，提高效率。
> * 各个分支之间互不影响。
>
> 创建分支的本质就是多创建一个指针：
>
> * 当前所在的分支由HEAD决定
> * HEAD会指向具体分支；分支会指向具体的版本

| 命令名称            | 功能作用                   |
| ------------------- | -------------------------- |
| git branch 分支名   | 创建分支                   |
| git branch -v       | 查看分支                   |
| git checkout 分支名 | 切换分支                   |
| git merge 分支名    | 将指定的分支合并到当前分支 |

### ==创建分支==

> * ```sh
>   ===git branch 分支名：即可完成分支的创建===
>   git branch hot-fix
>   ```

### ==查看分支==

> * ```sh
>   ===git branch -v：即可查看分支；*开头代表当前所在的分区，一般*后面的分支名为绿色---当前所在的分支===
>   git branch -v
>   ===可以看出创建的hot-fix分支是复制了一份master主分支的内容===
>     hot-fix ceec097 version 3.1 commit()
>   * master  ceec097 version 3.1 commit
>   ```

### ==切换分支&&修改分支==

> * ```sh
>   ===git checkout 分支名：即可进行分支之间的切换操作===
>   git checkout hot-fix
>   Switched to branch 'hot-fix'
>   ===再次查看分支会发现*移到了切换的分支上，且hot-fix的分支变为了绿色（并且此时命令行后面跟的也是（hot-fix））===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (hot-fix)
>   $ git branch -v
>   * hot-fix ceec097 version 3.1 commit
>     master  ceec097 version 3.1 commit
>   ```
>
> * 修改分支
>
>   * 在切换到某一个分支之后，对分支文件进行修改
>   * 修改完成，查看本地库状态：未添加暂存区，文件名报红
>   * 添加至暂存区
>   * 提交本地库
>   * 完成分支的修改操作。
>   * 此时查看分支的版本信息（历史版本），可以清楚看到当前指针指向的分支，当前分支指向的版本号
>   * ![image-20230704171515080](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230704171515080.png)

### ==合并分支==

> * ```sh
>   ===git merge 分支名：即可将指定的分支合并到当前分支===
>   ===此处是将hot-fix分支合并到当前分支master主线分支上===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git merge hot-fix
>   Updating ceec097..a125df8
>   Fast-forward
>    hellogit.txt | 2 --
>    1 file changed, 2 deletions(-)
>   ===分支完成后，查看当前分支文件：发现已经合并完成===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ cat hellogit.txt
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>   hello git~~~
>        ---Lixc
>    version 2.0
>   ```

### ==产生冲突&&解决冲突==

> * 冲突产生的表现：分支名后面状态为MERGING---
>
> * 冲突产生的原因：
>
>   * merge合并分支时，两个分支的同一文件都对文件进行不同的修改，Git无法决定使用哪一个，必须手动决定文件内容。
>
> * ```sh
>   ===当我们在不同的分支上对相同文件进行不同修改，并添加暂存区且提交本地库后；此时merge合并分支就会失败===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git merge hot-fix
>   Auto-merging hellogit.txt
>   CONFLICT (content): Merge conflict in hellogit.txt
>   Automatic merge failed; fix conflicts and then commit the result.
>
>   ===此时的命令行分支后面就会是：(分支名|MERGING)===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ git status
>   On branch master
>   You have unmerged paths.
>     (fix conflicts and run "git commit")
>     (use "git merge --abort" to abort the merge)
>   Unmerged paths:
>     (use "git add <file>..." to mark resolution)
>           both modified:   hellogit.txt
>   no changes added to commit (use "git add" and/or "git commit -a")
>
>   ===查看文件就会发现，Git默认将两个不同分支的文件内容进行了比对，呈现在了文件内容中，此时需要我们手动处理===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ cat hellogit.txt
>   <<<<<<< HEAD
>   master branch update
>                   Lixc
>   =======
>   hot-fix branch update
>                    Lixc
>   >>>>>>> hot-fix
>
>   ===手动对导致合并分支失败的文件进行处理；并添加暂存区且提交代码库===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ vim hellogit.txt
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ git add hellogit.txt
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ git commit -m "master&hot-fix branch merge" hellogit.txt
>   fatal: cannot do a partial commit during a merge.
>
>   ===上面使用“git commit -m "日志信息" 文件名”方式来提交代码库，就会异常，不能带有文件名===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master|MERGING)
>   $ git commit -m "master&hot-fix branch merge"
>   [master 3ac0a3d] master&hot-fix branch merge
>   ```
>
>
>   ===此时的命令行分支后面就只会是：(分支名)，查看本地库状态也是正常===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git status
>   On branch master
>   nothing to commit, working tree clean
>
>   ```
> 
>   ```







## Git团队协作机制

> 若想使用Git进行团队协作，就要使用到代码托管中心（GitHub、Gitee、GitLab...又叫做远程库）。
>
> * 团队内协作：
>   * 将自己本地库文件推送到远程库（git push）
>   * 其他人可从远程库将文件进行克隆复制到他们的本地库中（git clone），还可以将修改后的代码推送到远程库
>   * 再从远程库将别人修改后的最新版代码拉取到本地库中（git pull）
> * 跨团队协作：
>   * B团队将A团队的远程库代码fork（叉子）到B团队自己的远程库中
>   * B团队开发人员将B团队远程库代码clone到自己的本地库中
>   * 将修改完成的代码push推送到B团队的远程库中
>   * B团队的远程库代码更新完成，向A团队发送Pull Request
>   * A团队经由审核，在自己远程库下对B团队远程库进行merge合并操作
>   * A团队开发人员即可对最新的远程库进行pull拉取到本地库进行开发。





## IDEA集成Git

### 1. 配置Git忽略文件

> IDEA中每创建一个项目，总会有除去代码和pom等系列文件之外的与项目无关文件被创建，所以在IDEA集成Git管理项目时，要忽略掉无关文件。
>
> 为何要忽略：
>
> * 与项目实际开发功能无关，忽略掉这些无关文件能够屏蔽不同IDEA工具之间的差异。
>
> 如何忽略：
>
> * ==创建忽略规则文件==xxx.ignore（文件名随意，建议是==git.ignore==）
>
> * 忽略文件放置位置原则上在任何位置都可以，但为了方便让~/.gitconfig文件引用，建议==放置在家目录==下
>
> * ==git.ignore忽略文件模板内容==：
>
>   * ```ignore
>     # Compiled class file
>     *.class
>     
>     # Log file
>     *.log
>     
>     # BlueJ files
>     *.ctxt
>     
>     # Mobile Tools for Java (J2ME)
>     .mtj.tmp/
>     
>     # Package Files #
>     *.jar
>     *.war
>     *.nar
>     *.ear
>     *.zip
>     *.tar.gz
>     *.rar
>     
>     # virtual machine crash logs, see 
>     http://www.java.com/en/download/help/error_hotspot.xml
>     hs_err_pid*
>     
>     .classpath
>     .project
>     .settings
>     target
>     .idea
>     *.iml
>     ```
>
> * 在==.gitconfig文件中引用忽略配置文件==（自定义的git.ignore文件放置在Windows家目录中）
>
>   * ```gitconfig
>     [user]
>     	name = Lixc
>     	email = vc572936348@yeah.net
>     [credential]
>     	helper = manager
>     [http]
>     	sslVerify = false
>     注意使用/正斜线
>     [core]
>     	excludesfile = C:/Users/Administrator/git.ignore
>     ```






### 2. 定位Git程序

> File->Settings->Version Control->Git->Path to Git exe：此处配置git.exe安装目录
>
> * D:\PlantingTrees\java_install\3-Git\Git\bin\git.exe






### 3. 初始化本地库

> 由于新创建的maven项目需要被Git进行管理（Git是分布式版本控制系统），需要初始化本地库：
>
> * ~~Git命令行客户端指令方式是：git init（不再使用Git命令行客户端方式）~~
> * IDEA中进行本地库的初始化操作
>   * ~~VCS：Version Control Setting版本控制设置（我这里的IDEA 2020.3没有该选项）~~，使用Git
>   * Create Git Repository：默认选中的就是当前项目的根目录，ok即可。
> * 此时查看当前maven项目的磁盘目录下，会出现.git隐藏目录，表示Git初始化本地库成功





### 4. 添加暂存区

> Git命令行客户端指令：git add 文件
>
> 将IDEA创建的项目初始化本地库后：
>
> * pom文件变红色了，联系到Git中，正是文件尚未添加暂存区的状态（未被追踪）
>   * Git -> Add
>   * pom文件变为绿色，表示添加暂存区成功（文件被Git追踪），文件尚未提交本地库（git commit）
>
> * 在java目录下，新建文件
>   * IDEA智能弹出提示：是否添加暂存区--->选择Add添加（否则报红表示未被追踪到），且不再提示
> * 当然若是项目中很多文件都需要添加暂存区，直接在项目名右键：Git -> Add 添加暂存区即可
> * 忽略的文件是浅灰色状态





### 5. 提交本地库

> Git命令行客户端指令：git commit -m "日志信息" 文件
>
> 将IDEA创建的项目初始化本地库后，文件添加暂存区完成：
>
> * pom文件变为绿色，在Git中正是文件已添加暂存区未提交本地库的状态
>   * Git -> Commit Filte
>
> * 当然若是项目中很多文件都需要提交本地库，直接在项目名右键：Git -> Commit Directory 即可。
>   * 会看到当前添加暂存区的文件；选中
>   * 定义Commit Message：提交日志信息
>   * Commit即可
> * 现在所有被Git管理追踪的文件都由绿色变为了正常的黑色





### 6. 切换版本/版本穿梭

> Git命令行客户端指令：git reset --hard 精简版本号
>
> 将IDEA创建的项目初始化本地库后，文件添加暂存区、提交本地库完成：
>
> * 被Git管理的文件一旦修改，文件名就变为了蓝色
>   * git add 添加暂存区（注意不要让Git管理追踪忽略文件）
>   * git commit 提交本地库 
>     * 提交时就会看到上一次提交时的版本号
>   * 注意：
>     * IDEA中蓝色文件代表已追踪文件被修改，所以可以直接忽略添加暂存区的操作，直接右键提交本地库。
> * 查看版本
>   * IDEA2020版本双击Java文件即可查看历史版本
> * 切换版本
>   * 右键历史版本，Checkout Revision '历史版本号'



### 7. 创建分支

> Git命令行客户端指令：git branch 分支名
>
> IDEA中使用分支---创建分支
>
> * 右键项目名称（也可以点击IDEA右下角分支名）
>   * Git -> Branches New Branch
>     * 填写分支名称
>
> * 此时IDEA右下角看到分支名，说明分支创建成功，且当前已切换为新建分支



### 8. 切换分支

> Git命令行客户端指令：git checkout 分支名
>
> IDEA中使用分支---切换分支
>
> * 点击IDEA右下角当前分支名
>   * 可以看到每个分支，点击需要切换的分支 -> Checkout
> * IDEA右下角看到切换后的分支名



### 9. 合并分支

> Git命令行客户端指令：git merge 分支名
>
> IDEA使用分支---合并分支
>
> - 当前分支需要对另外的分支进行合并，点击当前分支
>   - 选择另外的分支---> Merge into Current（合并到当前分支）



### 10. 解决冲突

> Git命令行客户端指令：
>
> * git merge 分支名后，会出现问题（|MERGING）
> * 修改合并异常的文件
> * 添加暂存区、提交本地库
>
> IDEA使用分支---合并分支---代码冲突：
>
> * 当前分支需要对另外的分支进行合并，点击当前分支
>   - 选择另外的分支---> Merge into Current（合并到当前分支）
>   - 此时由于两个分支上的代码都做了修改，会弹出冲突提示框，点击Merge合并
>     - IDEA自动弹出未修改前代码、分支一修改代码、分支二修改代码
>       - 手动做出代码合并即可（X、《、》三个符号分别表示不使用该代码、使用右边代码、使用左边代码）















# GitHub

为了测试团队协作，创建了多个GitHub账号==

```sh
VVLixc
lz001214.

OtherPeopleVC
lz001214.
```



## 创建远程仓库

## 远程仓库操作

#### ==创建远程仓库别名==

> * ```sh
>   ===git remote -v：查看当前所有远程地址别名===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git remote -v
>   ===git remote add 别名(可以考虑使用远程库的库名) 远程地址：起别名===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git remote add git-01demo https://github.com/VVLixc/git-01demo.git
>   
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git remote -v
>   git-01demo      https://github.com/VVLixc/git-01demo.git (fetch)
>   git-01demo      https://github.com/VVLixc/git-01demo.git (push)
>   ```

#### ==推送本地库(本地分支)到远程库==

> * ```sh
>   ===git push 别名(或者直接使用链接) 分支：推送本地库到远程库===
>   ===这个连接重置了---推送失败===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git push git-01demo master
>   fatal: unable to access 'https://github.com/VVLixc/git-01demo.git/': Recv failure: Connection was reset
>   ===这次推送本地库到远程库成功了===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git push git-01demo master
>   info: please complete authentication in your browser...
>   Enumerating objects: 22, done.
>   Counting objects: 100% (22/22), done.
>   Delta compression using up to 8 threads
>   Compressing objects: 100% (13/13), done.
>   Writing objects: 100% (22/22), 1.81 KiB | 616.00 KiB/s, done.
>   Total 22 (delta 3), reused 0 (delta 0), pack-reused 0
>   remote: Resolving deltas: 100% (3/3), done.
>   To https://github.com/VVLixc/git-01demo.git
>    * [new branch]      master -> master
>   ```

#### ==拉取远程库内容==

> * ```sh
>   ===git pull 远程库地址别名(或直接使用链接) 远程分支名：将远程仓库对于分支的最新内容拉下来与当前分支直接合并===
>   ===拉取失败===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git pull git-01demo master
>   error: RPC failed; curl 28 Recv failure: Connection was reset
>   fatal: expected flush after ref listing
>   ===拉取成功===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git pull git-01demo master
>   remote: Enumerating objects: 5, done.
>   remote: Counting objects: 100% (5/5), done.
>   remote: Compressing objects: 100% (2/2), done.
>   remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
>   Unpacking objects: 100% (3/3), 678 bytes | 0 bytes/s, done.
>   From https://github.com/VVLixc/git-01demo
>    * branch            master     -> FETCH_HEAD
>      3ac0a3d..cd60519  master     -> git-01demo/master
>   Updating 3ac0a3d..cd60519
>   Fast-forward
>    hellogit.txt | 1 +
>    1 file changed, 1 insertion(+)
>   
>   ===查看当前Git版本信息（历史记录）===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ git reflog
>   cd60519 (HEAD -> master, git-01demo/master) HEAD@{0}: pull git-01demo master: Fast-forward
>   ...
>   ...
>   
>   ===查看从远程库拉取来的内容，已被更新===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-Space/git-01demo (master)
>   $ cat hellogit.txt
>   master branch update
>   hot-fix branch update
>                    Lixc
>   update data by github
>   ```

#### ==克隆远程仓库到本地==

> * 克隆远程仓库到本地库无需登录账号
>
> * clone会做如下操作：
>
>   * 拉取代码
>   * 初始化本地库
>   * 创建别名（一般默认创建的别名叫做origin）
>
> * ```sh
>   ===git clone 远程地址===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-OtherPeopleLocalRep
>   $ git clone https://github.com/VVLixc/git-01demo.git
>   Cloning into 'git-01demo'...
>   remote: Enumerating objects: 25, done.
>   remote: Counting objects: 100% (25/25), done.
>   remote: Compressing objects: 100% (12/12), done.
>   remote: Total 25 (delta 4), reused 21 (delta 3), pack-reused 0
>   Receiving objects: 100% (25/25), done.
>   Resolving deltas: 100% (4/4), done.
>   
>   ===进入到远程仓库克隆后的本地库内，查看本地库状态===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-OtherPeopleLocalRep/git-01demo (master)
>   $ git status
>   On branch master
>   Your branch is up to date with 'origin/master'.
>   
>   nothing to commit, working tree clean
>   
>   ===进入到远程仓库克隆后的本地库内，查看远程库别名---使用到了默认的origin别名===
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-OtherPeopleLocalRep/git-01demo (master)
>   $ git remote -v
>   origin  https://github.com/VVLixc/git-01demo.git (fetch)
>   origin  https://github.com/VVLixc/git-01demo.git (push)
>   
>   ```

#### ==GitHub团队内协作==

> * 多人协作开发
>
> * 其他开发人员
>
>   * ```sh
>     ===其他GitHub成员将克隆到他们本地库后的代码修改后进行push提交（前提是要添加到暂存区，提交到本地库）===
>     ===错误原因：需要远程仓库的管理者邀请（我这里来回切换两个账号来邀请和接受；并且还要对Windows系统的凭据管理器保有的GitHub账号来回删除确保在Git Bash Here命令行推送时的用户是其他用户不是管理者）===
>     Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-OtherPeopleLocalRep/git-01demo (master)
>     $ git push  https://github.com/VVLixc/git-01demo.git master
>     info: please complete authentication in your browser...
>     remote: Permission to VVLixc/git-01demo.git denied to OtherPeopleVC.
>     fatal: unable to access 'https://github.com/VVLixc/git-01demo.git/': The requested URL returned error: 403
>     
>     ===此时再对文件进行推送，成功===
>     Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/Git-OtherPeopleLocalRep/git-01demo (master)
>     $ git push  https://github.com/VVLixc/git-01demo.git master
>     info: please complete authentication in your browser...
>     Enumerating objects: 5, done.
>     Counting objects: 100% (5/5), done.
>     Delta compression using up to 8 threads
>     Compressing objects: 100% (2/2), done.
>     Writing objects: 100% (3/3), 295 bytes | 295.00 KiB/s, done.
>     Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
>     remote: Resolving deltas: 100% (1/1), completed with 1 local object.
>     To https://github.com/VVLixc/git-01demo.git
>        cd60519..1636062  master -> master
>     ```
>
> * 管理者
>
>   * ```sh
>     ===在GitHub上可以看到其他人员更新后的远程库最新版本===
>     ===将远程库内容拉取到本地仓库下===
>     git pull 别名 分支名
>     
>     ```



## 跨团队协作

### fork

将代码叉到自己远程库中

### Pull Requests

将修改后的代码发送给原作者那里

> 外团队：
>
> * New pull request
> * Create pull request
>
> 本团队：
>
> * Merge pull request
>   * Confirm merge
> * 此刻代码就被合并了



## SSH免密登录

> 访问远程库有两个链接：
>
> * HTTPS链接
> * SSH链接
>   * 默认无法使用：没有SSH keys
>   * 需要添加一个新的公开的key
>
> 实现SSH免密登录
>
> * 在Windows系统家目录下
>
>   * 若存在.ssh文件夹，删除
>
>   * 右键运行Git命令行客户端（Git Bash Here）来生成.ssh文件
>
>     * 利用非加密协议算法
>
>     * ```sh
>       ===运行命令生成.ssh秘钥目录===
>       ssh-keygen -t rsa -C GitHub账号邮箱
>       连续敲三次回车
>       ```
>
>     * 之后会生成公钥（.pub）和私钥，复制公钥
>
> * GitHub
>
>   * 账号下settings
>     * SSH and GPG keys
>       * New SSH keys
>
> * 之后Windows系统连接当前GitHub账号，就无需再反复输入账号密码
>
> * ```sh
>   ===设置.ssh文件并获取公钥===
>   Administrator@WIN-74FJNDUUU7O MINGW64 ~
>   $ ssh-keygen -t rsa -C vc572936348@yeah.net
>   Generating public/private rsa key pair.
>   Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
>   Created directory '/c/Users/Administrator/.ssh'.
>   Enter passphrase (empty for no passphrase):
>   Enter same passphrase again:
>   Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa
>   Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub
>   The key fingerprint is:
>   SHA256:65BfpIkbfSjNcEfScebD9wYQZyxpSBiChRq4TTUsMf4 vc572936348@yeah.net
>   The key's randomart image is:
>   +---[RSA 3072]----+
>   | .o+o+o .+o.=+o  |
>   |..+.+. ....*++.  |
>   | +.+    . o.+.o  |
>   |. o.     o   o o |
>   |    E . S o     o|
>   |       O B     . |
>   |      * O o      |
>   |       B o       |
>   |      . o        |
>   +----[SHA256]-----+
>
>   Administrator@WIN-74FJNDUUU7O MINGW64 ~
>   $ cd .ssh/
>
>   Administrator@WIN-74FJNDUUU7O MINGW64 ~/.ssh
>   $ cat id_rsa.pub
>   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDnkpB1Ez/Ko/jPKQQlCPfOeF7hUOfBccdATEDvxduQySqTfs/SQgcA5dnGdMayWZTAT47YOtLLyNCSkiUliKyFkNK530aNlcHyjEO1FYWREMG968AVOQYMyKpJChLiaX20g8sFdin9wjnwCmb/zcuoOfNUTyPV7Rxh3qrTGmfu0aiN9czVjqA6HJqZcPYJLDdNgcubqouSWyH0owxhyhrxk3z5pI6R/No9DUBy8FoIQ14NBDPYm4B9ppCSTN5afahhjNhEYqnoFa4V8YrqUfgxBRpwCDcH20AMFaJVr0jfDKdTOV19AqQFDc4b61/Z7bxF9EbjIkjYri89J+OBLgseJW4ncRFzbSb8ULoZzCtFfwVOqH4CdZeG1hLXXUJNapb+56jML7gait/vY6lg5tIeTBiPnUgr0Cm4RyEQjwoJ9rEwGNYonOfjGCKWLq4JmCxhBSYmYao7FuUgZnf1xnNuDb+5Nduvn0Mylm1YAv2+3gacBzLxyFXA/Id0RdVcaXU= vc572936348@yeah.net
>   ```
>
>   ===本地库测试通过SSH方式来拉取、推送代码===
>   ```
> Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ cat helloGit.txt
>   hello git
>           -Lixc 1.2
>           hot-fix 1.1
>           GitHub 1.0
>           OtherPeople 1.0
>           OutterTeam 1.0
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ git pull git@github.com:VVLixc/Git-Lixc.git master
>   The authenticity of host 'github.com (127.0.0.1)' can't be established.
>   ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
>   This key is not known by any other names.
>   Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
>   Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
>   remote: Enumerating objects: 5, done.
>   remote: Counting objects: 100% (5/5), done.
>   remote: Compressing objects: 100% (2/2), done.
>   remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
>   Unpacking objects: 100% (3/3), 689 bytes | 6.00 KiB/s, done.
>   From github.com:VVLixc/Git-Lixc
> 
>    * branch            master     -> FETCH_HEAD
>      Updating be47243..dc215e7
>        Fast-forward
>         helloGit.txt | 2 +-
>         1 file changed, 1 insertion(+), 1 deletion(-)
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ cat helloGit.txt
>   hello git
>           -Lixc 1.3
>           hot-fix 1.1
>           GitHub 1.0
>           OtherPeople 1.0
>           OutterTeam 1.0
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ vim helloGit.txt
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ git add helloGit.txt
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ git commit -m "Lixc 1.3：Test SSH key to push" helloGit.txt
>   [master 79fd0c4] Lixc 1.3：Test SSH key to push
>    1 file changed, 1 insertion(+), 1 deletion(-)
> 
>   Administrator@WIN-74FJNDUUU7O MINGW64 /d/PlantingTrees/java_install/3-Git/MyGit/Git-Lixc (master)
>   $ git push git@github.com:VVLixc/Git-Lixc.git master
>   Enumerating objects: 5, done.
>   Counting objects: 100% (5/5), done.
>   Delta compression using up to 8 threads
>   Compressing objects: 100% (2/2), done.
>   Writing objects: 100% (3/3), 333 bytes | 333.00 KiB/s, done.
>   Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
>   To github.com:VVLixc/Git-Lixc.git
>      dc215e7..79fd0c4  master -> master
>   ```





## IDEA集成GitHub

> * pulgin安装GitHub插件
>
> * Version Control：GitHub选项

### 1. 设置GitHub账号

> * Version Control：
>   * GitHub：右边+号
>     * 使用账号密码登录GitHub
>       * Server：github.com
>       * Login：账号
>       * Password：密码
>       * 使用账号密码登录GitHub非常难登录
>     * 登录框右上角Use Token口令登录
>       * GitHub账号Settings -> Developer settings -> Personal access tokens
>         * Generate new token
>           * 起个名字
>           * 将token口令权限全部赋予
>           * Generate token
>       * 复制生成的token口令（注意：该口令只会显示一次，之后刷新就不会显示，所以可以找个位置保存起来）
>         * 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
>       * 使用生成的token口令在IDEA设置GitHub账号



### 2. 分享工程到GitHub

> GitHub插件可直接将本地项目分享到GitHub代码托管中心。（无需再通过GitHub创建远程库，然后从IDEA将本地库推送到远程库）
>
> IDEA项目分享到GitHub：
>
> * Git -> GitHub -> Share project on GitHub
>   * 弹出分享框：
>     * Repository name：远程库名（和本地库名保持一致）
>     * Remote：远程链接别名（和本地库名保持一致）
>     * Description：描述
> * 此时就会将本地项目（已被Git管理）分享到GitHub



### 3. push推送本地库到远程库

> 有两种方式push：
>
> * HTTPS
> * SSH：更方便，避免登录
>
> push推送远程库操作：
>
> * 远程库别名默认使用的是HTTPS链接：
>   * 点击选中Define Remote 自定义远程地址链接
>     * 起别名 + SSH链接
> * 右键项目Git -> Push  或 Git ->Push 或 IDEA右上角 push 绿色向上箭头
>
> push将本地库代码推送到远程库，要向push成功，一定要保证本地库版本比远程库版本高，否则push拒绝；
>
> 所以成熟程序员需要在每次本地修改代码前，确认本地库代码更新到最新，再修改、提交、推送。



### 4. pull拉取远程库到本地库

> 本地库一定要和远程库代码保持一致，千万不要在pull拉取前乱搞，否则可能导致拉取失败
>
> pull：
>
> * 选择远程别名（建议使用SSH免密登录链接）
> * Branches to merge：分支合并记得勾选（若拉取过程需要合并分支会自动合并）
> * 拉取



### 5. clone克隆远程库到本地

> 在IDEA初始化界面可以直接Get from Version Control 
>
> * Version Control：版本控制工具选择Git
> * URL：选择GitHub远程库SSH链接
> * Directory：最后的项目名和远程库项目名保持一致





# Gitee码云

## 4. IDEA集成码云

> IDEA安装Gitee插件
>
> * Version Control -> Gitee ->添加码云账号
>
> 分享工程到Gitee：
>
> * Git -> Gitee -> Share project on Gitee





## 5. 码云复制GitHub项目

> Gitee码云可以直接复制GitHub项目，方便做项目的迁移和下载
>
> * 新建仓库
>   * 导入已有仓库
>     * 导入GitHub仓库
>   * 创建
>
> 复制完成后，GitHub项目做了更新
>
> * Gitee上点击项目名字后面的刷新图标即可（强制同步）







# GitLab

> 基于局域网的代码托管中心
>
> GitLab自建代码托管平台

## GitLab官网地址

>官网地址：https://about.gitlab.com/
>
>安装说明：https://about.gitlab.com/installation/



## GitLab安装

### 服务器准备





### 安装包准备





### 编写安装脚本





### 初始化GitLab服务





### 启动GitLab服务





### 使用浏览器访问GitLab





### GitLab创建远程库





### IDEA集成GitLab







![image-20230707183733453](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230707183733453.png)

# GitHub Desktop-Git客户端使用

## 仓库操作

> 新建仓库：
>
> * File + New Repository
> * Ctrl + N
>
> 删除仓库：
>
> * 左上角Current Repository点击扩展 + 选中仓库右键remove
>   * 勾选Also move this repository to Recycle Bin（同时将仓库移到回收站）
>     * GitHub Desktop客户端内仓库清除、本地库在磁盘真实文件夹清除--->回收站
>   * 不勾选
>     * 只会清除掉客户端中仓库，仓库在磁盘还是存在的，且可以通过拖拽方式将仓库重新添加到GitHub Desktop客户端内。



## 文件操作

> 新建文件：
>
> * 点击Show in Explorer，会进入到当前仓库在磁盘的位置
>   * 新建文件即可（在创建的本地仓库下，对文件及内容的新增、修改、删除操作都会被识别到）
> * 为何GitHub Desktop客户端可以识别到我们对文件的相关操作
>   * 用户操作文件，只是存储在了仓库下，
>   * 而存储文件的真实位置是在.git下---提交到本地库了
>   * 也就是工作区和本地库中间由GitHub Desktop客户端进行了文件比对（GitHub Desktop客户端也就相当于是暂存区）
>     * 例如会拿用户在工作区新建的文件，比对本地库中对应的文件（是否存在、是否更新、是否清除等等）
>     * 最终在客户端就可以看到我们对文件的相关操作
>
> 修改文件：
>
> * 在磁盘对应文件下对文件进行修改，客户端对应仓库中就会提示对应文件被修改了Modified
> * 点击提交
>
> 删除文件：
>
> * 若文件已经提交本地库后，误删除
>   * git restore 文件名 ：即可完成文件的恢复
>   * 但若是误删除后，还将版本进行了提交本地库操作
>     * git reset --hard 精简版本号
>     * git restore 文件名
>     * 切换版本，进行文件恢复
>       * 但该操作会导致上一版本消失
>     * git revert 精简版本号（当前版本或误删除后提交的版本，相当于是还原到该版本之前---新建版本）
>
> 历史版本：
>
> * 点击左侧History栏可以看到每一次对文件操作后提交到本地库的历史版本
>   * 左侧是提交简介
>   * 右侧包含
>     * 提交的日志信息及描述
>     * 提交版本号---由40位16进制的数字组成的版本号（SHA-1算法）（版本号也称作提交码）
>       * git cat-file -p 版本号
>         * 可以友好的看到文件内容
>     * ...



## 分支原理及操作

> 分支原理：
>
> * 分支说白了就是当前版本库的一个副本，开发人员就可以在副本上进行操作，最终再将分支（副本）合并即可
>
> * 可以想象若是没有分支，每一个开发人员都向版本库进行提交修改等操作，很容易造成冲突混乱，但有了分支（版本库的副本），只需要在每个开发人员专属的版本库副本进行文件的操作，最终提交到版本库即可
>   * 当然即便是利用分支来减少了代码提交的冲突，也不可避免在分支合并时的冲突，但是明显冲突次数大大减少。
>
> 分支操作：
>
> * 新建版本库的分支（副本）
>   * 创建本地库（版本库）后，默认存在主分支（master、main。。。可自定义主分支名）
>   * 点击客户端上方Current Branch（master）
>     * New Branch新建分支
> * 在每一个分支上进行开发
>   * 进入到某一个分支上，Show in Explorer进入分支对应的版本库副本，对文件进行操作（新增、修改、删除）
>     * 提交到当前分支即可
> * 分支合并
>   * 进入到主分支，进行分支合并
>     * 选中某个分支，进行分支的合并
>   * 合并冲突
>     * 合并分支时，两个分支都对同一文件进行了更新，只能手动进行merge
> * 创建分支并切换到该分支（创建分支是在本地库已有提交版本的前提下）
>   * git checkout -b 分支名
> * 删除分支
>   * git branch -d 分支名





## 标签

> 在History查看历史版本时：
>
> * 对分支的合并merge操作都是自动提交的，无法一眼见名知义
> * 所以可以对需要添加简介信息的历史版本（例如分支合并）创建Tag标签
>   * 选中某一历史版本，右键Create Tag
>   * 也可以对标签进行清除，右键Delete tag XXXXX(这里是自定义的标签内容)
>
> 为提交本地库版本增加标签，之后可以通过标签名进行访问：
>
> * git tag 标签名 版本号
> * 之后即可通过查看标签名来查看对应版本号及其之前的所有日志
>   * git log/reflog 标签名
>
> 删除标签
>
> * git tag -d 标签名



## 远程仓库-GitHub、Gitee

> GitHub：
>
> * File -> Clone repository 克隆远程库 -> 选择具体远程库 
>   * 本地库下修改远程库内容后 -> commit提交
>     * 推送到远程库：Push Origin 或 Ctrl + P
>   * 远程库修改内容后 
>     * 拉取到本地库：Fetch Origin后 + pull Origin 或 Ctrl + Shift + P



## README、IGNORE

> 



## 文件图标与比对功能

> 













