# Git对远程仓库的操作

---

### 查看远程仓库

查看远程仓库的指令

	git remote

前提:首先要有一个远程仓库,执行该命令后可以查看你对远程仓库定义的简写名称

	git remote show [remote-name]

查看远程仓库的更多信息

### 添加远程仓库

添加远程仓库命令

	git remote add <shortname> <url>

执行该命令为远程服务器定义简写名称,以后可直接使用简写名称拉取远程服务器中的内容
> 将远程仓库的内容克隆岛本地时,命令会自动将其添加为远程仓库并默认以 “origin” 为简写名称

### 从远程仓库获取与拉取

从远程仓库获取内容的命令

	git fetch [remote-name]

从远程仓库获取本地没有的数据,不会自动合并或修改本地的内容

	git pull

从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

### 推送到远程仓库

推送命令

	git push [remote-name] [branch-name]

### 对远程仓库的简写名称进行移除与重命名

对远程仓库的简写名称重命名指令

	git remote rename [oldname] [newname]

对远程仓库的简写名称进行移除的命令
	
	git remote rm [remote-name]

### 打标签

列出标签指令

	git tag

### 创建标签

Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。

附注标签是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息；但是如果你只是想用一个临时的标签，或者因为某些原因不想要保存那些信息，轻量标签也是可用的。

- 附注标签

		git tag -a v1.4 -m 'my version 1.4'

- 轻量标签

		git tag v1.4

> 在执行git show命令时,可以查看两种标签的展示信息的区别

### 后期打标签

附注标签是后期打标签的前提,因为后期打标签需要指定提交的校验和（或部分校验和）

	git tag -a v1.2 9fceb02

### 共享标签

默认情况下，git push 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。

	git push origin v1.5

### 检出标签

实际上是根据标签创建一个新的分支

	git checkout -b [branchname] [tagname]

### Git别名

Git提供的一个小技巧,让用户自定义命令,简化用户的输入

	git config --global alias.co checkout

配置以上命令后,以后需要执行checkout命令时,只需要执行如下命令

	git co


### 提交至远程仓库的一般流程

- 远程仓库为空,本地文件夹不为空时
	1. 在本地目标文件夹下初始化 `git init`
	2. 添加文件夹中的文件 `git add * `
	3. 提交至暂存区 `git commit -m "msg" `
	4. 与远程仓库建立链接 `git remote [shortname] [repo_url] `
	5. 提交至远程仓库 `git push [shortname] [branchname]`

- 远程仓库不为空,本地文件夹为空时
	1. 克隆远程仓库文件至本地文件夹 `git clone [repo_url]`,此时远程仓库和本地目标文件夹已建立链接
	2. 之后的操作参照上述的2,3,4,5

- 远程仓库和本地仓库都不为空时
	1. 参照1,2,3,4操作
	2. 拉去远程仓库中存在的文件,根据情况是否合并,合并拉取` git fetch`,不合并拉取`git pull`
	3. 提交至远程仓库 `git push [shortname] [branchname]`












