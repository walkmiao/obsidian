



# GIT学习笔记

[TOC]

`vcs`-**version control system 版本控制系统**

**git**属于`DVCS`即**distribute version control system  分布式版本控制系统**

## 常用git命令

### git clone

git clone  `git仓库地址` `文件夹名称(如不指定则以仓库名称命名)`

git clone 会默认创建一个提交，创建初始文件例如**.ignore**和**License**文件

### git pull

git pull = git fetch + git merge

### git branch

git branch `分支名称`  **创建分支**

git checkout `分支名称` **切换到已有分支**

git checkout -b `分支名称`  **创建并切换到分支**

git branch -d `分支名或SHA1前几位` **删除此分支 如果此分支还没有被merge过 则会拒绝删除**

git branch -D `分支名称或SHA1前几位` **强制删除此分支** *注意：分支也只是一个引用而已，删除分支并不会删除分支上的commits*

### git push

**git push的本质是上传当前支路的指向以及支路路径上的commits到远端仓库**

push的时候如果是本地的一个`分支`，需要指定`远程仓库名称以及分支`  例如如果本地当前在feature1分支上则为**git push origin feature1**

> 或者可以通过**git config**修改push.default来改变push的逻辑行为
>
> push的时候上传当前分支 但是并不上传HEAD指向 远程仓库的HEAD永远指向默认分支（在github上可以修改默认分支）
>
> ---
>
> **git push origin -d 分支名**  **删除远程分支**

### git merge

1. **特殊情况一  冲突处理**

   ![图1](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-15_17-13-21.jpg?raw=true)

   * 如果一个分支改了A文件另一个分支改了B文件 那么合并后就是又改A又改B

     ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-15_17-13-37.jpg?raw=true)

   * 如果一个分支改了A文件另一个分支也改了A文件 但是改的不是同一行 那么合并后就是都改

     ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-15_17-13-52.jpg?raw=true)

   * 如果一个分支改了A文件另一个分支也改了A文件 且改的是同一行 那么这时候merge就会产生冲突了

     在merge的时候git会提示相应的冲突信息 在A文件上会有两个冲突的标记 这时候需要选择一个改动作为最终的改动

     **选择完毕保存后 执行 git add A文件 并 git commit**

   * **放弃解决冲突， 取消Merge**

     `git merge --abort`  你的仓库就会回到merge前的状态

2. **特殊情况二 HEAD领先目标Commit**

   ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_08-42-04.jpg?raw=true)

   如果merge的时候Head领先目标commit,**且不存在分叉**，那么此时这个操作就是一个空操作

3. **特殊情况三 HEAD 落后目标Commit**

   ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_08-42-50.jpg?raw=true)

   ---

   ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_08-43-57.jpg?raw=true)

   

   如果HEAD落后目标commit 且不存在分叉 那么此时就是一个快速前移 **fast forward**

   这种情况经常出现 例如当你的同事提交新的commit到远端仓库，而你之后拉取远端仓库的时候 `git pull`

   ---

   ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_08-56-16.jpg?raw=true)

   ___

   ![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_08-56-38.jpg?raw=true)

   ---

   说一下这里的本地仓库 `origin/master`和`origin/HEAD`,他们是远端仓库在本地的镜像。

   当进行git pull的操作的时候 会进行两步走 

   1. 第一步 git fetch 会更新这两个本地镜像
   2. 第二步 merge目标的commit

   

### git diff

1. 比较暂存区和上一条提交 

   这条指定可以让你看到 如果你现在输入`git commit`将会提交什么

   `git diff --staged`

   `--staged 与 --cached 完全等价`

2. 比较工作目录和暂存区

   这条指定可以让你看到如果你输入`git add .`会像暂存区添加什么

   `git diff`

3. 比较工作目录和上一条提交

   是上面两条内容的相加, 即这条指令可以让你看到如果你此时`git add . `和`git commit`的话你将提交什么

   `git diff HEAD`

### rebase(变基)

**`git rebase `后面不仅仅可以接分支名称也可以接具体的commit`ID`**

例如 `git rebase 7f673`

以下内容引用自[sourceforge](https://git-scm.com/book/zh/v2/Git-分支-变基)

当开发任务分叉到两个不同的分支 又各自提交了更新

![](https://git-scm.com/book/en/v2/images/basic-rebase-1.png)



之前介绍过，整合分支最容易的方法是 `merge` 命令。 它会把两个分支的最新快照（`C3` 和 `C4`）以及二者最近的共同祖先（`C2`）进行三方合并，合并的结果是生成一个新的快照（并提交）。



![](https://git-scm.com/book/en/v2/images/basic-rebase-2.png)

其实，还有一种方法：你可以提取在 `C4` 中引入的补丁和修改，然后在 `C3` 的基础上应用一次。 在 Git 中，这种操作就叫做 *变基*。 你可以使用 `rebase` 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

在上面这个例子中，运行：

```console
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

它的原理是首先找到这两个分支（即当前分支 `experiment`、变基操作的目标基底分支 `master`）的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底 `C3`, 最后以此将之前另存为临时文件的修改依序应用。（译注：写明了 commit id，以便理解，下同）



**请注意进行变基后C4的commit将不在experiment分支上了,也就是个空提交，会被git的垃圾处理机制回收**



![](https://git-scm.com/book/en/v2/images/basic-rebase-3.png)

---

将 `C4` 中的修改变基到 `C3` 上

现在回到 `master` 分支，进行一次快进合并。

```console
$ git checkout master
$ git merge experiment
```

![master 分支的快进合并。](https://git-scm.com/book/en/v2/images/basic-rebase-4.png)


此时，C4' 指向的快照就和上面使用 merge 命令的例子中 C5 指向的快照一模一样了。 这两种整合方法的最终结果没有任何区别，但是变基使得提交历史更加整洁。 你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的，但它们看上去就像是串行的一样，提交历史是一条直线没有分叉。

一般我们这样做的目的是为了确保在向远程分支推送时能保持提交历史的整洁——例如向某个其他人维护的项目贡献代码时。 在这种情况下，你首先在自己的分支里进行开发，当开发完成时你需要先将你的代码变基到 origin/master 上，然后再向主项目提交修改。 这样的话，该项目的维护者就不再需要进行整合工作，只需要快进合并便可。

请注意，无论是通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。 变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。



### 更有趣的变基例子

在对两个分支进行变基时，所生成的“重放”并不一定要在目标分支上应用，你也可以指定另外的一个分支进行应用。 就像 [从一个特性分支里再分出一个特性分支的提交历史](https://git-scm.com/book/zh/v2/ch00/rrbdiag_e) 中的例子那样。 你创建了一个特性分支 `server`，为服务端添加了一些功能，提交了 `C3` 和 `C4`。 然后从 `C3` 上创建了特性分支 `client`，为客户端添加了一些功能，提交了 `C8` 和 `C9`。 最后，你回到 `server` 分支，又提交了 `C10`。

![从一个特性分支里再分出一个特性分支的提交历史。](https://git-scm.com/book/en/v2/images/interesting-rebase-1.png)

Figure 39. 从一个特性分支里再分出一个特性分支的提交历史

假设你希望将 `client` 中的修改合并到主分支并发布，但暂时并不想合并 `server` 中的修改，因为它们还需要经过更全面的测试。 这时，你就可以使用 `git rebase` 命令的 `--onto` 选项，选中在 `client` 分支里但不在 `server` 分支里的修改（即 `C8` 和 `C9`），将它们在 `master` 分支上重放：

```console
$ git rebase --onto master server client
```

以上命令的意思是：“取出 `client` 分支，找出处于 `client` 分支和 `server` 分支的共同祖先之后的修改，然后把它们在 `master` 分支上重放一遍”。 这理解起来有一点复杂，不过效果非常酷。

![截取特性分支上的另一个特性分支，然后变基到其他分支。](https://git-scm.com/book/en/v2/images/interesting-rebase-2.png)

Figure 40. 截取特性分支上的另一个特性分支，然后变基到其他分支

现在可以快进合并 `master` 分支了。（如图 [快进合并 master 分支，使之包含来自 client 分支的修改](https://git-scm.com/book/zh/v2/ch00/rrbdiag_g)）：

```console
$ git checkout master
$ git merge client
```

![快进合并 master 分支，使之包含来自 client 分支的修改。](https://git-scm.com/book/en/v2/images/interesting-rebase-3.png)

Figure 41. 快进合并 master 分支，使之包含来自 client 分支的修改

接下来你决定将 `server` 分支中的修改也整合进来。 使用 `git rebase [basebranch] [topicbranch]` 命令可以直接将特性分支（即本例中的 `server`）变基到目标分支（即 `master`）上。这样做能省去你先切换到 `server` 分支，再对其执行变基命令的多个步骤。

```console
$ git rebase master server
```

如图 [将 server 中的修改变基到 master 上](https://git-scm.com/book/zh/v2/ch00/rrbdiag_h) 所示，`server` 中的代码被“续”到了 `master` 后面。

![将 server 中的修改变基到 master 上。](https://git-scm.com/book/en/v2/images/interesting-rebase-4.png)

Figure 42. 将 server 中的修改变基到 master 上

然后就可以快进合并主分支 master 了：

```console
$ git checkout master
$ git merge server
```

至此，`client` 和 `server` 分支中的修改都已经整合到主分支里了，你可以删除这两个分支，最终提交历史会变成图 [最终的提交历史](https://git-scm.com/book/zh/v2/ch00/rrbdiag_i) 中的样子：

```console
$ git branch -d client
$ git branch -d server
```

![最终的提交历史。](https://git-scm.com/book/en/v2/images/interesting-rebase-5.png)

### 变基的风险

呃，奇妙的变基也并非完美无缺，要用它得遵守一条准则：

**不要对在你的仓库外有副本的分支执行变基。**

如果你遵循这条金科玉律，就不会出差错。 否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。

`变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交`。 如果你已经将提交推送至某个仓库，而其他人也已经从该仓库拉取提交并进行了后续工作，此时，如果你用 `git rebase` 命令重新整理了提交并再次推送，你的同伴因此将不得不再次将他们手头的工作与你的提交进行整合，如果接下来你还要拉取并整合他们修改过的提交，事情就会变得一团糟。

让我们来看一个在公开的仓库上执行变基操作所带来的问题。 假设你从一个中央服务器克隆然后在它的基础上进行了一些开发。 你的提交历史如图所示：

![克隆一个仓库，然后在它的基础上进行了一些开发。](https://git-scm.com/book/en/v2/images/perils-of-rebasing-1.png)

Figure 44. 克隆一个仓库，然后在它的基础上进行了一些开发

然后，某人又向中央服务器提交了一些修改，其中还包括一次合并。 你抓取了这些在远程分支上的修改，并将其合并到你本地的开发分支，然后你的提交历史就会变成这样：

![抓取别人的提交，合并到自己的开发分支。](https://git-scm.com/book/en/v2/images/perils-of-rebasing-2.png)

Figure 45. 抓取别人的提交，合并到自己的开发分支

接下来，这个人又决定把合并操作回滚，改用变基；继而又用 `git push --force` 命令覆盖了服务器上的提交历史。 之后你从服务器抓取更新，会发现多出来一些新的提交。

![有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交。](https://git-scm.com/book/en/v2/images/perils-of-rebasing-3.png)

Figure 46. 有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交

结果就是你们两人的处境都十分尴尬。 如果你执行 `git pull` 命令，你将合并来自两条提交历史的内容，生成一个新的合并提交，最终仓库会如图所示：

![你将相同的内容又合并了一次，生成了一个新的提交。](https://git-scm.com/book/en/v2/images/perils-of-rebasing-4.png)

Figure 47. 你将相同的内容又合并了一次，生成了一个新的提交

此时如果你执行 `git log` 命令，你会发现有两个提交的作者、日期、日志居然是一样的，这会令人感到混乱。 此外，如果你将这一堆又推送到服务器上，你实际上是将那些已经被变基抛弃的提交又找了回来，这会令人感到更加混乱。 很明显对方并不想在提交历史中看到 `C4` 和 `C6`，因为之前就是他把这两个提交通过变基丢弃的。

---



git merge会导致master分支和其他分支在合并的时候会出现分叉 rebase正是为了避免这种分叉

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-38-04.jpg?raw=true)

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-30-50.jpg?raw=true)

上图中执行`git merge branch1`分支后出现了分叉，而在branch1分支上执行`git rebase master`则是改变branch1分支父commit指向由`2变为4`重新提交其路线上的commits记录

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-31-12.jpg?raw=true)

这时候在切换到主分支`git checkout master`执行`git merge branch1`则会是一个快速前移动作`fast forward`

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-47-54.jpg?raw=true)

那么为什么不从master分支上执行rebase操作`git rebase  branch1`呢，如果这样执行的话 那么master分支上的commits已经不是之前的了，`3和4`两个commit已经在master上丢失了

**需要说明的是，rebase 是站在需要被 rebase 的 commit 上进行操作，这点和 merge 是不同的**

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-32-12.jpg?raw=true)

---



![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_10-32-44.jpg?raw=true)

---



### `git commit --amend`

当你发现最新的提交有问题需要修改的时候 不希望通过新增一个commit来实现 则可以使用这个命令 

`commit --amend`提交的时候不是新建一个commit而是把暂存区和上一个commit的内容合并创建一个新的commit并把当前的commit替换掉。

所以`commit --amend`要做的事就是对最新的一条commit进行修正



> **需要注意的有一点：commit --amend 并不是直接修改原 commit 的内容，而是生成一条新的 commit。并替换掉原有的最新的commit**

### `git rebase -i 交互式提交`

`rebase`的本意是变基,非常符合此操作的意思

如果需要修改的不是最新的提交而是之前的某个提交 那么`commit —amend`已经用不上了,但是可以用`rebase -i`来实现 

例如`git rebase -i HEAD^^`



> 说明：在 Git 中，有两个「偏移符号」： ^ 和 ~。
>
> ^ 的用法：在 commit 的后面加一个或多个 ^ 号，可以把 commit 往回偏移，偏移的数量是 ^ 的数量。
>
> 例如：master^ 表示 master 指向的 commit 之前的那个 commit； HEAD^^ 表示 HEAD 所指向的 commit 往前数两个 commit。
>
> ~ 的用法：在 commit 的后面加上 ~ 号和一个数，可以把 commit 往回偏移，偏移的数量是 ~ 号后面的数。例如：HEAD~5 表示 HEAD 指向的 commit往前数 5 个 commit。



上面这行代码表示，把当前 `commit` （ `HEAD` 所指向的 `commit`） `rebase` 到 `HEAD` 之前 2 个的 `commit` 上：

**编辑界面-选择commit和对应的操作**

输入上面的命令后会进入编辑界面，列出了从指定的commit到当前最新的commit 按照**由旧到新的顺序**

这两行指示了两个信息

1. 需要处理哪些commits
2. 怎么处理他们(pick/edit/squash等)

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_16-37-25.jpg?raw=true)

你需要修改这两行的内容来指定你的操作。每个commit默认的操作都是pick，表示直接应用这个操作。

所以如果你现在直接退出编辑界面，那么将是个空操作。

但你的目标如果是修改倒数第二个commit也就是最上面的那个commit  ->`add myfill.txt`,你就需要把它的操作指令由pick改为edit。edit的意思是应用这个commit,然后停下来等待自动修正。

**需要注意的是如果你需要更改列表中多个commit,即把多行的pick改为edit。那么处理顺序也是从上到下的。等每一个需要改变的commit执行完`git rebase --continue`之后才会处理下个commit**

---

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_16-38-03.jpg?raw=true)



**保存并退出编辑器后回到界面 输入 git log 你会发现当前最新的commit已经是`add myfill.txt`了**

![](https://github.com/walkmiao/PIC_SAVE/blob/master/git-learn/Xnip2019-07-16_16-45-19.jpg?raw=true)



此时修改myfill.txt文件后 先执行`git add`然后执行`git commit —amend`填入commit信息最后在执行

`git rebase --continue`



### git reset —hard 撤销最新的提交 

有的时候刚写的提交连自己都觉得有问题 需要重新写

`git reset --hard HEAD^` 

因为你要撤销最新的commit所以你只需要恢复到上一个commit也就是HEAD^

**需要注意的是撤销的那条commit并没有消失,如果你在撤销他之前记下了它的SHA-1码 那么你还可以通过SHA-1来找到它**



### git reflog

在使用Git的过程中，有时候会因为一些误操作，比如reset、rebase、merge等。特别是在Commit之后又执行了git reset --hard HEAD强制回滚本地记录以及文件到服务器版本，导致本地做的修改全部恢复到Git当前分支的服务器版本，同时自己的Commmit记录也消失了。碰到这种情况，不要慌，我们在Git上做的任何操作都只是在原来之前的操作上做修改，并且会被记录下来保存，也就是说无论你做了什么，对于Git来说都可以进行回滚操作。



只需要使用`git reset --hard SHA1记录编码`即可回滚到指定的commit

如果你需要对远端仓库也进行同步的话 那么必须使用`git push origin master -f`来进行强制提交。正式环境中慎用这个功能。





### 丢弃指定的提交的几种方法

* git rebase -i

  `git rebase -i`除了能够修改指定的提交外 还可以用做丢弃指定提交

  `git rebase -i HEAD^^` 在编辑器中显示倒数第二个commit到最新commit的列表。

  如果想丢弃其中一条commit 直接将那行删除即可。

* rebase --onto

  除了用交互式 `rebase` ，你还可以用 `rebase --onto` 来更简便地撤销提交。

  `rebase` 加上 `--onto` 选项之后，可以指定 `rebase` 的「起点」。一般的 `rebase`，告诉 Git 的是「我要把当前 `commit` 以及它之前的 `commits` 重新提交到目标 `commit` 上去，这其中，`rebase` 的「起点」是自动判定的：选取当前 `commit` 和目标 `commit` 在历史上的交叉点作为起点。

  除了用交互式 `rebase` ，你还可以用 `rebase --onto` 来更简便地撤销提交。

  `rebase` 加上 `--onto` 选项之后，可以指定 `rebase` 的「起点」。一般的 `rebase`，告诉 Git 的是「我要把当前 `commit` 以及它之前的 `commit`s 重新提交到目标 `commit` 上去，这其中，`rebase` 的「起点」是自动判定的：选取当前 `commit` 和目标 `commit` 在历史上的交叉点作为起点。

  例如下面这种情况：

  

  ![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe24400508e3c8?imageslim)

  

  如果在这里执行：

  ```
  git rebase 第3个commit
  ```

  那么 Git 会自动选取 `3` 和 `5` 的历史交叉点 `2` 作为 `rebase` 的起点，依次将 `4` 和 `5` 重新提交到 `3` 的路径上去。

  而 `--onto` 参数，就可以额外给 rebase 指定它的起点。例如同样以上图为例，如果我只想把 `5` 提交到 `3` 上，不想附带上 `4`，那么我可以执行：

  ```
  git rebase --onto 第3个commit 第4个commit branch1
  ```

  `--onto` 参数后面有三个附加参数：目标 `commit`、起点 `commit`（注意：rebase 的时候会把起点排除在外）、终点 `commit`。所以上面这行指令就会从 `4` 往下数，拿到 `branch1` 所指向的 `5`，然后把 `5` 重新提交到 `3` 上去。

  

  ![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe24400d7d73d0?imageslim)

  

  同样的，你也可以用 `rebase --onto` 来撤销提交：

  ```
  git rebase --onto HEAD^^ HEAD^ branch1
  ```

  上面这行代码的意思是：以倒数第二个 `commit` 为起点（起点不包含在 `rebase`序列里哟），`branch1` 为终点，`rebase` 到倒数第三个 `commit` 上。

  也就是这样：

  

  ![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe243fce5804fd?imageslim)

  

---

### git revert commitID/或者HEAD+偏移符号^~等

**增加**一个新的revert commit 来撤销其指定的`commitID`所做的操作。

注意是增加一个新的提交来撤销之前的某个提交 来达到相互抵消的效果

---

# 高级 7：reset 的本质——不止可以撤销提交

前面讲到，在最新的 `commit` 写错时，可以用 `reset --hard` 来把 `commit` 撤销：

```
git reset --hard HEAD^
```

> 用这行代码可以撤销掉当前 `commit`

在这节里，就对 `reset` 多说点，说说它的本质，说说它在撤销提交之外的用途。

## reset 的本质：移动 HEAD 以及它所指向的 branch

实质上，`reset` 这个指令虽然可以用来撤销 `commit` ，但它的实质行为并不是撤销，而是移动 `HEAD` ，并且「捎带」上 `HEAD` 所指向的 `branch`（如果有的话）。也就是说，`reset` 这个指令的行为其实和它的字面意思 "reset"（重置）十分相符：它是用来重置 `HEAD` 以及它所指向的 `branch` 的位置的。

而 `reset --hard HEAD^` 之所以起到了撤销 `commit` 的效果，是因为它把 `HEAD` 和它所指向的 `branch` 一起移动到了当前 `commit` 的父 `commit` 上，从而起到了「撤销」的效果：



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe19c8a3235853?imageslim)



> Git 的历史只能往回看，不能向未来看，所以把 `HEAD` 和 `branch` 往回移动，就能起到撤回 `commit` 的效果。

所以同理，`reset --hard` 不仅可以撤销提交，还可以用来把 `HEAD` 和 `branch` 移动到其他的任何地方。

```
git reset --hard branch2
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb605b0de?imageslim)



不过……`reset` 后面总是跟着的那个 `--hard` 是什么意思呢？

`reset` 指令可以重置 `HEAD` 和 `branch` 的位置，不过在重置它们的同时，对工作目录可以选择不同的操作，而对工作目录的操作的不同，就是通过 `reset` 后面跟的参数来确定的。

## reset --hard：重置工作目录

`reset --hard` 会在重置 `HEAD` 和 `branch` 的同时，重置工作目录里的内容。当你在 `reset` 后面加了 `--hard` 参数时，你的工作目录里的内容会被完全重置为和 `HEAD` 的新位置相同的内容。换句话说，就是你的未提交的修改会被全部擦掉。

例如你在上次 `commit` 之后又对文件做了一些改动：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb5a0e894?imageslim)



然后，你执行了 `reset` 并附上了 `--hard` 参数：

```
git reset --hard HEAD^
```

你的 `HEAD` 和当前 `branch` 切到上一条 `commit` 的同时，你工作目录里的新改动也一起全都消失了，不管它们是否被放进暂存区：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb5dbef68?imageslim)



可以看到，在 `reset --hard` 后，所有的改动都被擦掉了。

## reset --soft：保留工作目录

`reset --soft` 会在重置 `HEAD` 和 `branch` 时，保留工作目录和暂存区中的内容，并把重置 `HEAD` 所带来的新的差异放进暂存区。

什么是「重置 `HEAD` 所带来的新的差异」？就是这里：



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb5c6a249?imageslim)



由于 `HEAD` 从 `4` 移动到了 `3`，而且在 `reset` 的过程中工作目录的内容没有被清理掉，所以 `4` 中的改动在 `reset` 后就也成了工作目录新增的「工作目录和 `HEAD` 的差异」。这就是上面一段中所说的「重置 `HEAD` 所带来的差异」。

所以在同样的情况下：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb5a0e894?imageslim)



假设此时当前 `commit` 的改动内容是新增了 `laughters.txt` 文件：

```
git show --stat
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb7cdd727?image)



如果这时你执行：

```
git reset --soft HEAD^
```

那么除了 `HEAD` 和它所指向的 `branch1` 被移动到 `HEAD^` 之外，原先 `HEAD` 处 `commit` 的改动（也就是那个 `laughters.txt` 文件）也会被放进暂存区：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb7e6e40b?image)



这就是 `--soft` 和 `--hard` 的区别：`--hard` 会清空工作目录的改动，而 `--soft` 则会保留工作目录的内容，并把因为保留工作目录内容所带来的新的文件差异放进暂存区。

## reset 不加参数：保留工作目录，并清空暂存区

`reset` 如果不加参数，那么默认使用 `--mixed` 参数。它的行为是：保留工作目录，并且清空暂存区。也就是说，工作目录的修改、暂存区的内容以及由 `reset` 所导致的新的文件差异，都会被放进工作目录。简而言之，就是「把所有差异都混合（mixed）放在工作目录中」。

还以同样的情况为例：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb5a0e894?image)



> 修改了 `games.txt` 和 `shopping list.txt`，并把 `games.txt` 放进了暂存区。

```
git show --stat
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333cb7cdd727?image)



> 最新的 `commit` 中新增了 `laughters.txt` 文件。

这时如果你执行无参数的 `reset`：

```
git reset HEAD^
```

工作目录的内容和 `--soft` 一样会被保留，但和 `--soft` 的区别在于，它会把暂存区清空：

```
git status
```



![img](https://user-gold-cdn.xitu.io/2017/11/22/15fe333d086f9754?image)



## 小结

本节内容讲了 `reset` 指令的本质：重置 `HEAD` 以及它所指向的 `branch` 的位置。同时，介绍了 `reset` 的三种参数：

1. `--hard`：重置位置的同时，清空工作目录的所有改动；
2. `--soft`：重置位置的同时，保留工作目录和暂存区的内容，并把重置 `HEAD` 的位置所导致的新的文件差异放进暂存区。
3. `--mixed`（默认）：重置位置的同时，保留工作目录的内容，并清空暂存区。

除了上面这三种参数，还有一些没有列出的较为不常用的参数；另外除了我讲的功能外，`reset` 其实也还有一些别的功能和用法。不过 `reset` 最关键的功能、用法和本质原理就是上面这些了，想了解更多的话，可以去官网了解一下

---

### git stash

**stash:临时存放工作目录的改动**

"stash" 这个词，和它意思比较接近的中文翻译是「藏匿」，是「把东西放在一个秘密的地方以备未来使用」的意思。在 Git 中，`stash` 指令可以帮你把工作目录的内容全部放在你本地的一个独立的地方，它不会被提交，也不会被删除，你把东西放起来之后就可以去做你的临时工作了，做完以后再来取走，就可以继续之前手头的事了。

具体说来，`stash` 的用法很简单。当你手头有一件临时工作要做，需要把工作目录暂时清理干净，那么你可以：

```
git stash
```

就这么简单，你的工作目录的改动就被清空了，所有改动都被存了起来。

然后你就可以从你当前的工作分支切到 `master` 去给你的同事打包了……

打完包，切回你的分支，然后：

```
git stash pop
```

你之前存储的东西就都回来了。很方便吧？

> 注意：没有被 track 的文件（即从来没有被 add 过的文件不会被 stash 起来，因为 Git 会忽略它们。如果想把这些文件也一起 stash，可以加上 `-u` 参数，它是 `--include-untracked` 的简写。就像这样：

```
git stash -u
```

---

### .gitignore文件



一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件模式。 来看一个实际的例子：

```console
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件 `.gitignore` 的格式规范如下：

- 所有空行或者以 `＃` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（`!`）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]`匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`*`) 表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` , `a/b/z` 或 `a/b/c/z` 等。

我们再看一个 .gitignore 文件的例子：

```
# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```

| Tip  | GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表，你可以在 https://github.com/github/gitignore 找到它. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |













