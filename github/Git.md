- [[#获取Git仓库|获取Git仓库]]
		- [[#自己创建仓库|自己创建仓库]]
		- [[#克隆仓库|克隆仓库]]
- [[#记录更新|记录更新]]
		- [[#忽略文件|忽略文件]]
		- [[#查看具体修改|查看具体修改]]
		- [[#提交更新|提交更新]]
- [[#移除文件|移除文件]]
- [[#撤销操作|撤销操作]]
		- [[#git commit误操作|git commit误操作]]
		- [[#git add误操作|git add误操作]]
- [[#远程仓库使用|远程仓库使用]]
		- [[#从远程仓库抓取|从远程仓库抓取]]
		- [[#推送到远程仓库|推送到远程仓库]]
		- [[#查看远程仓库|查看远程仓库]]
- [[#Git分支|Git分支]]
		- [[#创建分支|创建分支]]
		- [[#切换分支|切换分支]]
		- [[#分支创建、删除和合并|分支创建、删除和合并]]
- [[#分支命令|分支命令]]
- [[#远程协作|远程协作]]
- [[#拉取|拉取]]
		- [[#删除远程分支|删除远程分支]]


安装完Git之后做的第一件事就是设置用户名称和邮件地址，每一个Git提交都会使用这些信息，并且写入到每一次提交中，且不可更改，使用的代码如下：
```shell
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```
如果要针对特定项目使用不同的用户名称与邮件地址，那么就在那个项目目录下运行没有 `--global`的命令来配置。

可以使用
```shell
git config --list
```
来列出所有的配置
![](images/Pasted%20image%2020231205170743.png)




# 获取Git仓库
有两种方法，一种是在现有项目下导入所有文件到Git中去，第二种是从一个服务器克隆一个Git仓库。


### 自己创建仓库
我们可以在现有目录中初始化仓库，只需要进入该项目目录并输入
```shell
git init
```
这样会在当前目录下创建一个 **.git**的子目录，里面的文件是git仓库的必须文件，是Git仓库的骨干。

然后使用
```shell
git add *.c
```
来对指定文件进行跟踪，然后执行
```shell
git commit -m 'initial project version'
```
提交。

现在就得到了一个实际维护（跟踪）着很多个文件的Git仓库。

### 克隆仓库
如果要获得一份已经存在了的Git仓库的拷贝，就需要使用 `git clone`命令。Git克隆的是该Git仓库服务器上的几乎所有数据，当我们执行 `git clone`命令时，远程Git仓库中的每一个文件的每一个版本都将被拉取下来。命令是：
```shell
git clone [url]
git clone https://github.com/libgit2/libgit2
```
上面这个就会在当前目录创建一个名为libgit2的目录，然后在这个目录下初始化一个 `.git`文件夹，从远程仓库拉取下所有数据放入`.git`文件夹，然后从中读取最新版本的文件的拷贝。
如果要自定义本地仓库的名字，那么使用
```shell
git clone https://github.com/libgit2/libgit2 mylibgit
```


# 记录更新

`git add`是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“添加内容到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。
每个文件都不外乎这两个状态： **已跟踪**和**未跟踪**，已跟踪是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间之后，它们的状态可能处于 **未修改**、**已修改**和 **已放入暂存区**，未跟踪文件既不存在于上次快照的记录中，也没有放入暂存区。初次克隆某个仓库时，所有文件都是已跟踪文件，和未修改状态。

只要编辑了某些文件，Git就将它们标记为已修改文件，然后我们将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复，
![](images/Pasted%20image%2020231205173642.png)
文件的状态就如上图。

如果要查看文件处于什么状态，使用
```shell
git status
```

如果自己创建一个新的README文件，然后使用git status，会得到如下信息：
```shell
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)
```
可以看到README处于未跟踪状态。

要跟踪这个文件，使用
```shell
git add README
```
然后会发现状态变成了：
```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
```
只要在 `Changes to be committed` 这行下面的，就说明是已暂存状态。

如果是修改了一个已跟踪的文件，那么会得到这个信息：
```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```
文件 `CONTRIBUTING.md` 出现在 `Changes not staged for commit` 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。 要暂存这次更新，需要运行 `git add` 命令。
```shell
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```
如果此时再修改暂存的文件，那么会变成这样：
```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```
`CONTRIBUTING.md`文件同时出现在暂存区和非暂存区。实际上 Git 是暂存了上面的版本， 如果你现在提交，那么这个文件的版本就是上面的版本，而不是下面的版本。


如果需要简化状态信息，那么就使用 
```bash
git status -s
git status --short
```
输出会变成这样：
```bash
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
`??`是未跟踪文件，`A`是暂存区文件，`M`是修改后的文件，左边的`M`是指文件被修改且放进了暂存区，右边的`M`是指文件被修改了但是还没放入暂存区。


### 忽略文件
如果有文件不要纳入Git的管理，也不想它们一直出现在未跟踪的列表，通常都是自动生成的文件，例如日志文件，此时我们可以创建一个 `.gitignore`的文件，在里面写我们要忽略的文件模式：
```bash
$ cat .gitignore
*.[oa]
*~
```
第一行是让Git忽略所有的以 `.o`和`.a`结尾的文件。第二行让Git忽略所有以波浪符结尾的文件。
要养成一开始就设置好`.gitignore`的习惯，以免误提交这些没用的文件，具体的写法可以需要时问ChatGpt。

### 查看具体修改
`git status`只能查看文件修改了，但是具体修改了什么并不知道，这里就可以用`git diff`命令，它可以通过文件补丁的格式显示具体哪些行发生了改变，

直接使用`git diff`是比较当前文件和暂存区域快照之间的差异，也就是修改之后还**没有暂存**起来的变化内容。
```bash
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

若要查看**已暂存**（即已加入暂存区，但尚未提交）的更改与最后一次提交时的差异，就使用`git diff --cached`

### 提交更新
使用
```bash
git commit
```
来提交更新

也可以跳过暂存区，将所有的跟踪文件全部提交，就使用
```bash
git commit -a
```

# 移除文件
如果到了暂存区，但是又不想提交这个文件了，就可以使用
```bash
git rm <filename>
```
命令来完成，同时从工作目录删除这个文件，也就是磁盘中没有这个文件了。


但是如果要从暂存区移除，但是还希望保留在当前工作目录，也就是让文件保留在磁盘，但是不想让Git继续跟踪，常见于： **忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时**
```bash
git rm --cached <filename>
```
使用上面这个命令即可。



# 撤销操作
### git commit误操作
有时提交后，发现有的文件还没修改，但是我们还是只想有一个提交信息而不是两个，这时候就可以撤销重新提交，使用
```bash
git commit --amend
```
会将暂存区中的文件提交，最终只会有一个提交，第二次的提交会覆盖第一次提交的结果，
```bash
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```




### git add误操作
你已经修改了两个文件并且想要将它们作为两次独立的修改提交，但是却意外地输入了 `git add *` 暂存了它们两个。 如何只取消暂存两个中的一个呢？
```bash
git reset HEAD <file>
```


如果你修改了一个文件并希望撤销这些修改，使文件恢复到上一次提交的状态，可以使用：
```bash
git restore <file>
```
这会丢弃工作目录中对指定文件的**未暂存**更改。
这其实相当于只是拷贝了另一个文件来覆盖它，所以是危险的行为。

记住，在 Git 中任何 _已提交的_ 东西几乎总是可以恢复的。 甚至那些被删除的分支中的提交或使用 `--amend` 选项覆盖的提交也可以恢复 。 然而，任何你未提交的东西丢失后很可能再也找不到了。



# 远程仓库使用
远程仓库是指托管在因特网或其他网络中的项目的版本库。
如果要查看已经配置的远程仓库服务器，使用
```bash
git remote -v
```

如果要添加远程仓库：
```bash
git remote add <shortname> <url>
```


### 从远程仓库抓取
要从远程仓库中获取数据，就执行
```
git fetch <URL>
```
执行后将会拥有这个远程仓库中所有分支的引用，可以随时合并或查看。


### 推送到远程仓库
要分享自己的项目，必须将其推送到上游，使用
```bash
git push origin master
```
这里假设是将master分支推送到 origin服务器，origin是Url的别名，在克隆时自动设置的。

### 查看远程仓库
查看一个远程仓库的更多信息：
```bash
git remote show [remote-name]
```
信息如下
```bash
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/schacon/ticgit
  Push  URL: https://github.com/schacon/ticgit
  HEAD branch: master
  Remote branches:
    master                               tracked
    dev-branch                           tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```
可以当前正处于 master分支，如果运行`git pull`就会抓取所有远程引用，将远程的master分支合并到本地的master分支。


# Git分支
Git记录的是文件的快照，而不是文件之间的差异。
在第一次提交时，Git会保存一个提交对象，这个对象包含指向所有文件当前状态的（快照）的指针。
提交对象包含文件快照的元数据和一个指向上一次提交对象的指针，如果一个文件上一次提交后未更改，那么Git不会再次保存该文件，而是简单地链接到之前存储的文件快照。
假设现在有一个工作目录，现在有三个要被暂存和提交的文件，**暂存**会为每一个文件计算校验和，然后就把当前版本的文件快照保存到Git仓库（使用Blob对象）
使用`git commit`进行提交操作时，Git先计算每个子目录的校验和，然后在Git仓库中把校验和保存为树对象，然后Git创建一个提交对象，除了包含指针和快照指针外，还包含指向这个树对象（项目根目录）的指针，这样Git就能在需要时重现此次保存的快照。

**这里快照就是 树对象+Blob对象**

现在Git仓库中就有五个对象，三个是Blob对象（保存文件快照），一个树对象（记录目录结构和Blob对象索引）以及一个提交对象（包含指向树对象指针和所有提交信息）
![](images/Pasted%20image%2020231206204100.png)

如果修改后再提交，那么就变成这样：
![](images/Pasted%20image%2020231206205036.png)
这里快照`Snapshot`就是Blob对象+树对象

> 虽然Git的Blob对象用于存储文件的内容，但Git采取了多种措施来优化存储效率，减少内存和磁盘空间的占用：**压缩**、**去重**、**增量存储**、**打包**
> 去重： 如果多个文件或文件的多个版本完全相同，Git只会存储一个Blob对象。例如，如果你复制了一个文件，Git不会再次存储该文件的内容，而是重用已有的Blob对象
> 
> 虽然理论上存储大量Blob对象可能看起来会占用大量内存和存储空间，但由于Git的这些优化措施，实际上它是非常高效的，特别是在处理大型项目时。


**Git的分支**，本质上只是指向提交对象的可变指针。Git的默认分支是master（现在是 **main**），默认分支指针会在每次的提交操作中自动向前移动，然后HEAD指针指向当前工作的分支。
> master（main）并不是一个特殊的分支，只是因为`git init`默认会创建这个分支
![](images/Pasted%20image%2020231206212230.png)


### 创建分支
Git创建一个新分支只是创建了一个可以移动的新的指针。使用的命令是：
```bash
git branch <new branch>
```
然后再当前的 提交对象 上创建一个指针：
![](images/Pasted%20image%2020231206213140.png)

HEAD这个指针指向当前所在的本地分支，可以想象为当前分支的别名，创建了一个新的分支，但是仍然在master分支上，![](images/Pasted%20image%2020231206213302.png)


### 切换分支
要切换到一个已存在的分支，就使用`git checkout`，会让HEAD指针指向切换的分支
```
git checkout testing
```
就会切换HEAD指针到testing去。
![](images/Pasted%20image%2020231206215347.png)

切换分支后，我们的HEAD会随着当前分支移动，原有的分支保持原地不动
![](images/Pasted%20image%2020231206220231.png)
使用
```bash
git checkout master
```
切回去后就变成这样了：
![](images/Pasted%20image%2020231206220300.png)
它是做了两件事，一是让HEAD指回到`master`分支，二是将工作目录恢复到`master`分支指向的快照内容。

现在再对master这边的分支修改后提交commit，就会产生分叉：
![](images/Pasted%20image%2020231207103754.png)

这与过去大多数版本控制系统形成了鲜明的对比，它们在创建分支时，将所有的项目文件都复制一遍，并保存到一个特定的目录。 完成这样繁琐的过程通常需要好几秒钟，有时甚至需要好几分钟。所需时间的长短，完全取决于项目的规模。而在 Git 中，任何规模的项目都能在瞬间创建新分支。 同时，由于每次提交都会记录父对象，所以寻找恰当的合并基础（译注：即共同祖先）也是同样的简单和高效。 这些高效的特性使得 Git 鼓励开发人员频繁地创建和使用分支。



### 分支创建、删除和合并
现在是当前的Git仓库，![](images/Pasted%20image%2020231207104721.png)
然后我们想在新分支上工作：
```bash
git branch iss53
git checkout iss53
```
就变成这样：
![](images/Pasted%20image%2020231207105230.png)

然后现在做了修改且提交了，就变成
![](images/Pasted%20image%2020231207105413.png)

但是现在，需要回到`master`分支去解决某些问题，解决后还需要让iss53这边的问题也解决掉，怎么做呢？

首先应该把当前分支的所有未提交修改都提交一遍，然后就切换到`master`
```bash
git checkout master
```
解决问题应该也用一个新分支防止直接修改`master`，这里创建一个新的分支`hotfix`，
```bash
git checkout -b hotfix
```
然后把`master`的问题解决后，再提交，变成这样：
![](images/Pasted%20image%2020231207110305.png)
`hotfix`是修改了`master`问题的提交，那么我们想`master`指向新的修改了问题的`hotfix`，可以使用 `git merge`
```bash
git checkout master
git merge hotfix
```
当前 `master` 分支所指向的提交是你当前提交（有关 hotfix 的提交）的直接上游，所以 Git 只是简单的将指针向前移动。 换句话说，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），所以现在就变成了这样:
![](images/Pasted%20image%2020231207111847.png)
因为`hotfix`的任务已经完成，所以可以删除了，那么使用 `-d`删除
```bash
git branch -d hotfix
```
然后回到`iss53`继续工作，后面变成这样：
![](images/Pasted%20image%2020231207112018.png)
现在的分隔越来越大了，应该要把这两个分支合起来。

现在就要将`iss53`合并到`master`分支，使用
```bash
git checkout master
git merge iss53
```
可以看到，命令和前面合并`hotfix`是一样的，只是运行信息会不太一样。
Git所做的工作是：
![](images/Pasted%20image%2020231207112307.png)
找到两个分支的末端所指的快照和两个分支的工作祖先，做一个简单的三方合并，然后三方合并的结果做了一个新的快照，并自动创建一个新的提交对象指向快照，这就叫做**合并提交**，![](images/Pasted%20image%2020231207112452.png)

但是有时候合并操作并不会这么顺利，可能在这两个不同的分支中，对一个文件的同一个部分进行了不同的修改，Git就不能干净地合并它们，就会发生冲突
发生冲突后，可以使用`git status`命令查看冲突的文件是什么：
```bash
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

    both modified:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```
比如上面这里就是`index.html`出现了冲突，打开这个文件，会出现下面这些内容：
```
<<<<<<< HEAD
这里是 main 分支的内容
=======
这里是 feature 分支的内容
>>>>>>> feature
```
修改这里的内容，比如改成：
```
这里是 main 分支和 feature 分支结合后的内容
```
然后使用 `git add index.html` 将更改标记为已解决。
最后，运行 `git commit` 完成合并


# 分支命令
```bash
git branch
```
是显示所有分支列表
```bash
$ git branch
  iss53
* master
  testing
```
前面带`*`就是HEAD指向的分支。


# 远程协作
假设网络中有一个`git.ourcompany.com`的Git服务器，如果从这里克隆，那么
```bash
git clone
```
会将这个远程仓库自动命名为`origin`（然后可以方便地拉取或推送），然后拉取它的所有数据，创建一个指向远程仓库的`master`分支指针，本且在本地将其命名为`origin/master`，它表示远程`master`指针地当前状态。然后Git还会在本地仓库创建一个`master`分支，这个本地分支与远程的`master`指向同一个提交对象。本地的 `master` 分支是你开始工作的基础，你可以在其上进行更改和提交，具体可以看下图：
![](images/Pasted%20image%2020231207164709.png)

如果此时其他人推送新的修改到了远程仓库，自己本地也做了修改，就变成这样：
![](images/Pasted%20image%2020231207165022.png)

那么为了同步工作，运行
```bash
git fetch origin
```
这里origin指的就是服务器，从里面抓取本地没有的数据，本且更新本地数据库，让远程指针`origin/master`指向新的、更新后的位置
![](images/Pasted%20image%2020231207165607.png)
那么现在就是本地出现了分支。这个分支情况可以按照前面的方式解决。


如果想要公开分享一个分支，需要将其推送到有写入权限的远程仓库中。使用的命令是
```bash
git push <remote> <branch>
```
比如
```bash
git push origin serverfix
```
但是这里的问题是Git自动将serverfix名字展开成了`refs/heads/serverfix:refs/heads/serverfix`，这意味着推送的本地的`serverfix`将会更新远程仓库的 `serverfix`分支，等价于了
```bash
git push origin serverfix:serverfix
```
如果我们不想让远程仓库上的分支叫做 `serverfix`，那么可以运行
```bash
git push origin serverfix:awesomebranch
```
来将本地的`severfix`分支推送到远程仓库上的`awesomebranch`分支。

其他协作者从服务器抓取时，就会在本地生成一个远程分支`origin/serverfix`，指向服务器的 `serverfix`分支的引用，但是本地不会自动生成一个可以编辑的副本，也就是不会有新的`serverfix`分支，只有一个不可修改的`origin/serverfix`指针，如果想要在这个分支上工作，那么可以这样：
```bash
git checkout -b serverfix origin/serverfix
```


# 拉取
当使用
```bash
git fetch
```
从服务器抓取本地没有的数据时，它不会修改工作目录里的内容，只会获取数据然后让我们自己合并。
但是
```bash
git pull
```
这个命令在大多数情况下做的是：
```bash
git fetch
git merge
```

### 删除远程分支
假设你已经通过远程分支做完所有的工作了 - 也就是说你和你的协作者已经完成了一个特性并且将其合并到了远程仓库的 `master` 分支（或任何其他稳定代码分支）。 可以运行带有 `--delete` 选项的 `git push` 命令来删除一个远程分支。 如果想要从服务器上删除 `serverfix` 分支，运行下面的命令：
```bash
git push origin --delete serverfix
```
这个命令做的只是从服务器上移除这个指针














