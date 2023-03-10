# Git的认识

## Git的好处

廉价的本地库（电脑上的磁盘）、方便的暂存区域和多个工作流分支

- 免费、开源分布式版本控制系统
- 快速高效处理从小型到大型的各种项目
- 易于学习，占地面积小，性能极快

---



## 版本控制

### 概念

记录文件内容变化，以便用于查看特定版本的内容（修订）情况。-、

### 好处

就是可以记录文件修改的历史记录，从而可以查看历史版本，方便版本切换。

---



## 版本控制系统

### 集中式

如SVN

- 优点：有权限者可通过中央查看他人的版本修改；管理员管理单一系统也比较容易（管理员无需管理各个客户端上的版本）
- 缺点：中央服务器的单点故障（此时所有人都丧失了版本控制的功能）

### 分布式

如Git

- 服务器断网的情况下也可以进行开发（因为版本控制是在本地进行的）
- 每个客户端保存的也都是整个完整的项目（包含历史记录，更加安全），但同时你本地保存的内容占用也大了。

### 总结

分布式的特点是效率高，占用大，也就是说在你的本地仓库里有一份完整项目的备份，并且拥有全部权限，本地具备版本控制能力。

而集中式则是需要提交不同的版本到中央集中开发，本地上一般只有个别版本的备份，且不具备版本控制的能力。

---

## 代码托管中心

即远程库。

是基于网络服务器的远程代码仓库。 

- 局域网：GitLab
- 互联网：GitHub（外网）、Gitee 码云（国内网站）

---



## Git工作机制

- 工作区：代码存放的磁盘目录的位置

  就是你在电脑里能看到的目录，比如一个包含.git的文件夹就是一个工作区：

- 暂存区：在工作区完成后把代码**添加**到暂存区（临时存储，还没有生成历史版本）

- 本地库（版本库）：将暂存区代码commit（提交）到本地库，此时会生成历史版本。.git文件夹就是版本库。

- 远程库：将本地库内的版本推送到远程库中

工作区---add--->暂存区---commit--->本地库---push--->远程库

远程库---pull--->本地库

每一个版本都是基于上一个版本的，因此无法跨过当前版本删除历史版本。





Git管理的文件分为：工作区，版本库，版本库又分为暂存区stage和暂存区分支master(仓库)

工作区>>>>暂存区>>>>仓库

git add把文件从工作区>>>>暂存区，git commit把文件从暂存区>>>>仓库，

在commit之前，对文件进行修改都是可以的。并且修改过后就必须重新add，尽管该文件已经add进暂缓区中。

一旦commit之后，就上传到一个不可修改的库中，上传的文件都会设置为一个版本保存下来。



---

# Git操作

## Git基本操作

![img](https://img-blog.csdnimg.cn/fbc4fbcf32034c90b33e9ed71ce63e9c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQmFvYmFv5bCP5YyF,size_20,color_FFFFFF,t_70,g_se,x_16)

### 设置用户签名conflg

基本语法：git config --global user.name 用户名    							git config --global user.email 邮箱

 签名的作用是区分不同操作者身份。用户的签名信息在每一个版本的提交信息中能够看 到，以此确认本次提交是谁做的。

 Git 首次安装必须设置一下用户签名，否则无法提交代码。

>  注意：这里设置用户签名和将来登录 GitHub（或其他代码托管中心）的账号没有任 何关系。



### 初始化本地库init

基本语法：**git init**



### 查看本地库状态status

基本语法：**git status**

默认分支时master

首次查看本地库的状态分析：

1. On branch master，即当前分支处于master；
2. No commit yet,即当前没有任何提交的文件；（判断是否有历史版本，本地库是否做到版本控制）
3. nothing to commit 没有任何文件（对于暂缓区，实际上就是起到提醒 提交文件到本地库 以保证版本控制 的作用）

当具有文件时，即工作区中不为空时，第3点信息改变，其中文件名是**红色**代表还被未追踪，绿色代表被追踪，即文件是否add到暂缓区中。



### 工作区-->暂存区add

基本语法：**git add 文件名**					git add .  （”.“表示所有文件）



### 将缓存区中的文件撤离rm

语法：git rm --cached <file>。将文件从暂存区删除，工作区还是存在

git rm --cached  :仅从暂存区删除

 git rm  :从暂存区与工作目录同时删除





### -->提交本地库commit

基本语法：**git commit -m “日志信息” **；对于日志信息必须填，且内容每次都不同。可以一次提交很多文件.

提交后，每个版本都对应一个版本号

- 当使用`git commit`而不使用`git commit -m`命令（没有带`-m`参数）时，会进入到vim编辑器中。
- git -commit -m 'first commit' //从暂存区提交 -m：注释
  git commit -a -m 'full commit'从工作区提交

### 查看版本log

**git log**：

1. 可看到当前分支详细版本信息，
2. 且看到提交者、提交日期。
3. 可以查看当前分支提交历史，以便确定要回退到哪个版本。
4. 可看到当前HEAD的指向、最近一次推送本地库文件到远程库时的版本

**git reflog**：

1. （可显示前七位的精简版本号）查看命令历史，以便确定要回到未来的哪个版本。
2. 记录被HEAD指针指向的分支的操作过程，即记录你的每一次命令。
3. 可看到当前HEAD的指向、最近一次推送本地库文件到远程库时的版本

#### `git log` 的退出

- 当`commit`（提交）比较多，`git log` 的内容在一页显示不完整，满屏放不下的时候，就会显示冒号。
- 回车（往下滚一行）、空格（往下滚一页）可以继续查看剩余内容。
- 退出：英文状态下 按 `q` 可以退出`git log` 状态。

当指针HEAD指向该版本号时，cat 文件名可以获取该版本号的文件内容



### 版本穿梭reset

基本语法：**git reset --hard** 版本号

在.git隐藏文件中，有一个HEAD文件，里面保存了它指向的地址

在文件夹refs/heads中就有一个master文件，里面就保存了当前的Head所指向的版本号。



**<u>实际上就是HEAD指针指向当前分支，当前分支指向具体版本，也就是两个指针进行控制。</u>**



### 查看文件版本区别（diff）

- git diff 查看**工作区和暂存区**差异，

  如果此时暂存区为空，那么稍微有点不同，即：

  1.  暂存区为空使用git diff：因为此时暂存区为空，此时使用git diff同样也是比较工作区和仓库，即和使用git diff HEAD结果相同
  2.  暂存区不为空使用git diff:因为此时暂存区不为空，此时使用git diff比较的就是工作区和暂存区

- git diff --cached查看**暂存区和仓库**差异，

- git diff HEAD 查看**工作区或和仓库**的差异，其中：HEAD代表的是最近的一次commit的信息。



### 撤销修改

git restore --staged <file>: 撤销 暂存区，取消add

git restore <file>: 撤销 本地修改，恢复为版本库版本。亦可用于找回删除文件，即与git checkout -- <file>没啥区别




### 删除文件

git checkout -- 文件名，将误删的文件恢复到最新版本。

**<u>git checkout -- 文件名本质就是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。</u>**

#### 注意

你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

## GIt分支操作

分支底层是指针的引用。所以切换分支的本质就是移动 HEAD 指针。

### 好处

- 同时并行推进多个功能开发，提高开发效率。

-  各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败 的分支删除重新开始即可。

### 操作介绍

| 命令名称               | 作用                         |
| ---------------------- | ---------------------------- |
| git branch 分支名      | 添加分支                     |
| git checkout -b 分支名 | 新建一个分支，并切换到该分支 |
| git branch -v          | 查看分支                     |
| git checkout 分支名    | 切换分支                     |
| git merge 分支名       | 把指定的分支合并到当前分支   |
| git branch -d 分支名   | 删除分支                     |



### 合并产生冲突

- 冲突产生的表现：后面状态为 MERGING
- 冲突产生的原因：合并分支时，两个分支在**同一个文件的同一个位置**有两套完全不同的修改。Git 无法替 我们决定使用哪一个。必须**人为决定**新代码内容。

### 解决冲突

1. 编辑有冲突的文件，删除特殊符号，决定要使用的内容 。
   1. **特殊符号：<<<<<<< HEAD 当前分支的代码 ======= 合并过来的代码 >>>>>>> hot-fix**
2. 将修改好的文件添加到暂存区
3. 执行提交（注意：此时使用 git commit -m命令时**不能带文件名**,即只需要：git commit -m "日志信息"）

### 细节

- 当前分支的工作区不干净时，无法切换分支
- 不同分支下的同一文件不互通，即打开同一文件时，对应分支打开对应版本。



## 远程库

### 添加远程库

基本语法：**git remote add** **别名 远程地址**；别名一般都为origin

**git remote -v** 查看当前所有远程地址别名

### 删除远程库

可以用git remote rm <name> 命令

### 推送本地分支到远程仓库

基本语法：**git push 别名 分支名**

当本地库中没有版本更新时，无法推送

### 拉取远程库到本地

基本语法：**git pull 别名 分支名**

**拉取动作会自动提交到本地库**，远程库代码会同步到本地库代码，即本地库与远程库内容一致

拉取的动作必须在链接远程库的基础上进行

### 克隆远程库到本地

基本语法：**git clone 远程地址**；该动作也进行了来连接远程库

clone 会做如下操作：

- 拉取代码。
- 初始化本地仓库。
- 创建别名





# Git提交规范

**Git提交规范**

**<type>(<scope>):  <subject>**

注意冒号 : 后有空格

如 feat(miniprogram): 增加了小程序模板消息相关功能

标题分为 **类型** type、 **改动范围scope** 、 **精简总结subject** 三部分：

规范的主要类型如下：

| 类型     | 描述                                                         |
|---|---|
|**feat**|新功能或功能变更相关|
|**fix**|修复bug相关|
|docs|改动了文档，注释相关|
|style|修改了代码格式化相关，如删除空格、改变缩进、单双引号切换、增删分号等，并不会影响代码逻辑|
|refactor|重构代码，代码结构的调整相关（理论上不影响现有功能）|
|**perf**|性能改动，性能、页面等优化相关|
|test|增加或更改测试用例，单元测试相关|
|build| 影响编译的更改相关，比如打包路径更改、npm过程更改等|
|ci|持续集成方面的更改。现在有些build系统喜欢把ci功能使用yml描述。如有这种更改，建议使用ci|
|chore|其它改动相关，比如文件的删除、构建流程修改、依赖库工具更新增加等|
|revert|回滚版本相关|

其实实际开发中最常用的就是 feat、fix 和 perf，git提交基本上都是实现需求，更改bug，性能优化。除了上述这些主要

类型，我们也可以根据团队要求定制类型，毕竟规范是死的，人是活的嘛。比如为了大家更易读，我们只留几个常用的，

并且全改成中文，如：

功能更改：新功能或功能变更相关

修复bug：修复bug相关

优化：性能改动，性能、页面等优化相关

没有好与不好之分，适合团队的就是最好的！
