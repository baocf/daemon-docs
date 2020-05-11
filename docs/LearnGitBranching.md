## LearnGitBranching

#### 主要

##### 基础篇

循序渐进的介绍Git主要命令

- Git Commit

- Git Branch

  ​        指向某个提交纪录； 即使创建再多分的支也不会造成储存或内存上的开销，并且按逻辑分解工作到不同的分支要比维护那些特别臃肿的分支简单多；

- Git Merge

  ​       合并两个分支时会产生一个特殊的提交记录，它有两个父节点，翻译成自然语言相当于：“我要把这两个父节点本身及它们所有的祖先都包含进来。”

- Git Rebase

  ​       取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。Rebase 的优势就是可以创造更线性的提交历史。如果只允许使用 Rebase 的话，代码库的提交历史将会变得异常清晰。

##### 高级篇

​	要开始介绍Git的超棒特性了，快来吧

​	HEAD 是一个对当前检出记录的符号引用 —— 也就是指向你正在其基础上进行工作的提交记录；

​	HEAD 总是指向当前分支上最近一次提交记录。大多数修改提交树的 Git 命令都是从改变 HEAD 的指向开始的；

​	HEAD 通常情况下是指向分支名的（如 bugFix）。在你提交时，改变了 bugFix 的状态，这一变化通过 HEAD 变得可见。

​	如果想看 HEAD 指向，可以通过 `cat .git/HEAD` 查看， 如果 HEAD 指向的是一个引用，还可以用 	`git symbolic-ref HEAD` 查看它的指向

- 分离HEAD

  

  分离的 HEAD 就是让其指向了某个具体的提交记录而不是分支名。在命令执行之前的状态如下所示：

  ```HEAD -> master -> C1```

  HEAD 指向 master， master 指向 C1

- 相对引用1（^）

  相对引用非常给力，这里我介绍两个简单的用法：

  1. 使用 `^` 向上移动 1 个提交记录

     `master^` 相当于“`master` 的父节点”。

     `master^^` 是 `master` 的第二个父节点

  2. 使用 `~<num>` 向上移动多个提交记录，如 `~3`

    

- 相对引用2（~）

  ###### 强制修改分支位置

  我使用相对引用最多的就是移动分支。可以直接使用 `-f` 选项让分支指向另一个提交。例如:

  ```
  git branch -f master HEAD~3
  ```

  上面的命令会将 master 分支强制指向 HEAD 的第 3 级父提交。`-f` 则容许我们将分支强制移动到那个位置.

- 撤销变更

  1. `git reset`  通过把分支记录回退几个提交记录来实现撤销改动。你可以将这想象成“改写历史”。`git reset` 向上移动分支，原来指向的提交记录就跟从来没有提交过一样, **用于本地分支中使用**;

     

  2. `git revert` 可以把你的更改推送到远程仓库与别人分享， **用于远程分支中使用**; 我们要撤销的提交记录后面居然多了一个新提交！这是因为新提交记录 `C2'` 引入了**更改** —— 这些更改刚好是用来撤销 `C2` 这个提交的。也就是说 `C2'` 的状态与 `C1` 是相同的

     

##### 移动提交记录

​		自由修改提交树

​		到现在我们已经学习了 Git 的基础知识 —— 提交、分支以及在提交树上移动。 这些概念涵盖了 Git 90% 的功能，同样也足够满足开发者的日常需求

​		然而, 剩余的 10% 在处理复杂的工作流时(或者当你陷入困惑时）可能就显得尤为重要了。接下来要讨论的这个话题是“整理提交记录” —— 开发人员有时会说“我想要把这个提交放到这里, 那个提交放到刚才那个提交的后面”, 而接下来就讲的就是它的实现方式，非常清晰、灵活，还很生动;



- Git Cherry-Pick

  `git cherry-pick <提交号>...` 

   如果你想将一些提交复制到当前所在的位置（`HEAD`）下面的话， Cherry-pick 是最直接的方式了

  ```git cherry-pick C5 C3 C2``` 在当前分支基础上加上 C5, C3, C2 Hash Commiter.

-  交互式 rebase

  ​		当你知道你所需要的提交记录（**并且**还知道这些提交记录的哈希值）时, 用 cherry-pick 再好不过了 —— 没有比这更简单的方式了。

  ​		但是如果你不清楚你想要的提交记录的哈希值呢? 幸好 Git 帮你想到了这一点, 我们可以利用交互式的 rebase —— 如果你想从一系列的提交记录中找到想要的记录, 这就是最好的方法了

  

  ​		交互式 rebase 指的是使用带参数 `--interactive` 的 rebase 命令, 简写为 `-i`

  ​		如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

  当 rebase UI界面打开时, 你能做3件事:

  1. 调整提交记录的顺序（通过鼠标拖放来完成）
  2. 删除你不想要的提交（通过切换 `pick` 的状态来完成，关闭就意味着你不想要这个提交记录）
  3. 合并提交。简而言之，它允许你把多个提交记录合并成一个。

  ***Git 严格按照你在对话框中指定的方式进行了复制***

   `git rebase -i HEAD~4`   按照调整你想要的顺序及提交项.

##### 杂项

​		Git 技术、技巧与贴上大集合

**	本地栈式提交**

​		来看一个在开发中经常会遇到的情况：我正在解决某个特别棘手的 Bug，为了便于调试而在代码中添加了一些调试命令并向控制台打印了一些信息。

​		这些调试和打印语句都在它们各自的提交记录里。最后我终于找到了造成这个 Bug 的根本原因，解决掉以后觉得沾沾自喜！

​		最后就差把 `bugFix` 分支里的工作合并回 `master` 分支了。你可以选择通过 fast-forward 快速合并到 `master` 分支上，但这样的话 `master` 分支就会包含我这些调试语句了。你肯定不想这样，应该还有更好的方式……

- 只取一个提交记录

  ​		实际我们只要让 Git 复制解决问题的那一个提交记录就可以了。跟之前我们在“整理提交记录”中学到的一样，我们可以使用

  - `git rebase -i`
  - `git cherry-pick`

  来达到目的

- 提交的技巧 #1

  ​		接下来这种情况也是很常见的：你之前在 `newImage` 分支上进行了一次提交，然后又基于它创建了 `caption` 分支，然后又提交了一次。

  ​		此时你想对的某个以前的提交记录进行一些小小的调整。比如设计师想修改一下 `newImage` 中图片的分辨率，尽管那个提交记录并不是最新的了。

  我们可以通过下面的方法来克服困难：

  - 先用 `git rebase -i` 将提交重新排序，然后把我们想要修改的提交记录挪到最前

  - 然后用 `git commit --amend` 来进行一些小修改

  - 接着再用 `git rebase -i` 来将他们调回原来的顺序

  - 最后我们把 master 移到修改的最前端（用你自己喜欢的方法），就大功告成啦！

    ​		当然完成这个任务的方法不止上面提到的一种（我知道你在看 cherry-pick 啦），之后我们会多点关注这些技巧啦，但现在暂时只专注上面这种方法。 最后有必要说明一下目标状态中的那几个`'` —— 我们把这个提交移动了两次，每移动一次会产生一个 `'`；而 C2 上多出来的那个是我们在使用了 amend 参数提交时产生的，所以最终结果就是这样了。

  也就是说，我在对比结果的时候只会对比提交树的结构，对于 `'` 的数量上的不同，并不纳入对比范围内



- 提交的技巧 #2

  ​		我们可以使用 `rebase -i` 对提交记录进行重新排序。只要把我们想要的提交记录挪到最前端，我们就可以很轻松的用 `--amend` 修改它，然后把它们重新排成我们想要的顺序。

  ​		但这样做就唯一的问题就是要进行两次排序，而这有可能造成由 rebase 而导致的冲突。下面还是看看 `git cherry-pick`是怎么做的吧。

  ##### 要在心里牢记 ```cherry-pick``` 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。

- Git Tag

  相信你已经发现了：分支很容易被人为移动，并且当有新的提交时，它也会移动。分支很容易被改变，大部分分支还只是临时的，并且还一直在变。

  你可能会问了：有没有什么可以*永远*指向某个提交记录的标识呢，比如软件发布新的大版本，或者是修正一些重要的 Bug 或是增加了某些新特性，有没有比分支更好的可以永远指向这些提交的方法呢？

  Git 的 tag 就是干这个用的啊，它们可以（在某种程度上 —— 因为标签可以被删除后重新在另外一个位置创建同名的标签）永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。

  更难得的是，它们并不会随着新的提交而移动。你也不能检出到某个标签上面进行修改提交，它就像是提交树上的一个锚点，标识了某个特定的位置。

  咱们先建立一个标签，指向提交记录 `C1`，表示这是我们 1.0 版本。

  ```git tag v1 C1```

  我们将这个标签命名为 `v1`，并且明确地让它指向提交记录 `C1`，如果你不指定提交记录，Git 会用 `HEAD` 所指向的位置

  

  ##### git tag   -d  v1.0   删除本地的tag 

  ##### git push origin :refs/tags/v1.0   //删除远程的tag

  

- Git Descibe

  由于标签在代码库中起着“锚点”的作用，Git 还为此专门设计了一个命令用来**描述**离你最近的锚点（也就是标签），它就是 `git describe`！

  Git Describe 能帮你在提交历史中移动了多次以后找到方向；当你用 `git bisect`（一个查找产生 Bug 的提交记录的指令）找到某个提交记录时，或者是当你坐在你那刚刚度假回来的同事的电脑前时， 可能会用到这个命令。

  `git describe` 的语法是：

  ```
  git describe <ref>
  ```

  `<ref>` 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会以你目前所检出的位置（`HEAD`）。

  它输出的结果是这样的：

  ```
  <tag>_<numCommits>_g<hash>
  ```

  `tag` 表示的是离 `ref` 最近的标签， `numCommits` 是表示这个 `ref` 与 `tag` 相差有多少个提交记录， `hash` 表示的是你所给定的 `ref` 所表示的提交记录哈希值的前几位。

  当 `ref` 提交记录上有某个标签时，则只输出标签名称

##### 高级话题

只为真正的勇士

- 多次Rebase

- 二个父节点

  ###### 选择父提交记录

  操作符 `^` 与 `~` 符一样，后面也可以跟一个数字。

  但是该操作符后面的数字与 `~` 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交。还记得前面提到过的一个合并提交有两个父提交吧，所以遇到这样的节点时该选择哪条路径就不是很清晰了。

  Git 默认选择合并提交的“第一个”父提交，在操作符 `^` 后跟一个数字可以改变这一默认行为

  ###### 如果不加数字修改符直接检出 `master^`，会回到第一个父提交记录

  使用 `^` 和 `~` 可以自由地在提交树中移动，非常给力

  git checkout HEAD~; git checkout HEAD^2; git checkout HEAD~2;

  #### 等价于

  git checkout HEAD~^2~2;  //链式操作

​	   使用`git branch bugWork`加上一个目标提交记录来创建消失的引用



- 纠缠不清的分支

  现在我们的 `master` 分支是比 `one`、`two` 和 `three` 要多几个提交。出于某种原因，我们需要把 `master` 分支上最近的几次提交做不同的调整后，分别添加到各个的分支上。

  `one` 需要重新排序并删除 `C5`，`two` 仅需要重排排序，而 `three` 只需要提交一次。

- 

#### 远程

##### Push & Pull   - Git远程仓库

是时候分享你的代码了，让编码变得社交化吧

- Git Clone

  作用是在**本地**创建一个远程仓库的拷贝.

- 远程分支

  远程分支有一个特别的属性，在你检出时自动进入分离 HEAD 状态。Git 这么做是出于不能直接在这些分支上进行操作的原因, 你必须在别的地方完成你的工作, （更新了远程分支之后）再用远程分享你的工作成果

  远程分支有一个命名规范 —— 它们的格式是:

  - `<remote name>/<branch name>`

  因此，如果你看到一个名为 `o/master` 的分支，那么这个分支就叫 `master`，远程仓库的名称就是 `o`。

  大多数的开发人员会将它们主要的远程仓库命名为 `origin`，并不是 `o`。这是因为当你用 `git clone` 某个仓库时，Git 已经帮你把远程仓库的名称设置为 `origin` 了

  不过 `origin` 对于我们的 UI 来说太长了，因此不得不使用简写 `o` :) 但是要记住, 当你使用真正的 Git 时, 你的远程仓库默认为 `origin`!

  **o/master` 只有在远程仓库中相应的分支更新了以后才会更新**

- Git Fetch

  Git 远程仓库相当的操作实际可以归纳为两点：向远程仓库传输数据以及从远程仓库获取数据；

  `git fetch` 完成了仅有的但是很重要的两步:

  - 从远程仓库下载本地仓库中缺失的提交记录
  - 更新远程分支指针(如 `o/master`)

  `git fetch` 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。

  远程分支反映了远程仓库在你**最后一次与它通信时**的状态，`git fetch` 就是你与远程仓库通信的方式了，

  `git fetch` 通常通过互联网（使用 `http://` 或 `git://` 协议) 与远程仓库通信。

  ##### `git fetch` 并不会改变你本地仓库的状态。它不会更新你的 `master` 分支，也不会修改你磁盘上的文件。

  ##### 理解这一点很重要，因为许多开发人员误以为执行了 `git fetch` 以后，他们本地仓库就与远程仓库同步了。它可能已经将进行这一操作所需的所有数据都下载了下来，但是**并没有**修改你本地的文件

- Git Pull

  当远程分支中有新的提交时，你可以像合并本地分支那样来合并远程分支。也就是说就是你可以执行以下命令:

  - `git cherry-pick o/master`
  - `git rebase o/master`
  - `git merge o/master`

  实际上，由于先抓取更新再合并到本地分支这个流程很常用，因此 Git 提供了一个专门的命令来完成这两个操作。它就是我们要讲的 `git pull`。

   `git pull` 就是 git fetch 和 git merge <just-fetched-branch> 的缩写

- 模拟团队协作

- Git Push

  `git push` 负责将**你的**变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录。

  **注意 —— git push 不带任何参数时的行为与 Git 的一个名为 push.default 的配置有关。它的默认值取决于你正使用的 Git 的版本**

- 偏离的提交历史

  困难来自于远程库提交历史的**偏离**

  （历史偏离）有许多的不确定性，Git 是不会允许你 `push` 变更的。实际上它会强制你先合并远程最新的代码，然后才能分享你的工作;

  如果我们在 push 之前做 rebase 呢？

  git fetch, git rebase o/master, git push

  用 `git fetch` 更新了本地仓库中的远程分支，然后用 rebase 将我们的工作移动到最新的提交记录下，最后再用 `git push` 推送到远程仓库。

  

  还可以使用 `merge`

  尽管 `git merge` 不会移动你的工作（它会创建新的合并提交），但是它会告诉 Git 你已经合并了远程仓库的所有变更。这是因为远程分支现在是你本地分支的祖先，也就是说你的提交已经包含了远程分支的所有变化。

  用 `git fetch` 更新了本地仓库中的远程分支，然后**合并**了新变更到我们的本地分支（为了包含远程仓库的变更），最后我们用 `git push` 把工作推送到远程仓库



##### 		 `git pull` 就是 fetch 和 merge 的简写，类似的 `git pull --rebase` 就是 fetch 和 rebase 的简写！



​      git pull --rebase; git push   //拉取远程仓库后，线型合并完成后，推送发布远程；





- 锁定的Master(Locked Master)

  远程服务器拒绝!(Remote Rejected)

  如果你是在一个大的合作团队中工作, 很可能是master被锁定了, 需要一些Pull Request流程来合并修改。如果你直接提交(commit)到本地master, 然后试图推送(push)修改, 你将会收到这样类似的信息:

  ```
  ! [远程服务器拒绝] master -> master (TF402455: 不允许推送(push)这个分支; 你必须使用pull request来更新这个分支.)
  ```



###### 	为什么会被拒绝?

​	远程服务器拒绝直接推送(push)提交到master, 因为策略配置要求 pull requests 来提交更新.

​	你应该按照流程,新建一个分支, 推送(pu

sh)这个分支并申请pull request,但是你忘记并直接提交给了master.现在你卡住并且无法推送你的更新.

###### 	解决办法

新建一个分支feature, 推送到远程服务器. 然后reset你的master分支和远程服务器保持一致, 否则下次你pull并且他人的提交和你冲突的时候就会有问题.



##### 关于origin和它的周边 -Git远程仓库高级操作

做一名仁慈的独裁者一定会很有趣......



- 推送主分支

  ###### 合并特性分支

  既然你应该很熟悉 fetch、pull、push 了，现在我们要通过一个新的工作流来测试你的这些技能。

  在大型项目中开发人员通常会在（从 `master` 上分出来的）特性分支上工作，工作完成后只做一次集成。这跟前面课程的描述很相像（把 side 分支推送到远程仓库），不过本节我们会深入一些.

  但是有些开发人员只在 master 上做 push、pull —— 这样的话 master 总是最新的，始终与远程分支 (o/master) 保持一致。

  对于接下来这个工作流，我们集成了两个步骤：

  - 将特性分支集成到 `master` 上
  
  - 推送并更新远程分支
  
    
  
  ```git pull --rebase; git push```
  
  我们执行了两个命令:
  
  - 将我们的工作 rebase 到远程分支的最新提交记录
  - 向远程仓库推送我们的工作

​	

- 合并远程分支

  在开发社区里，有许多关于 merge 与 rebase 的讨论。以下是关于 rebase 的优缺点：

  优点:

  - Rebase 使你的提交树变得很干净, 所有的提交都在一条线上

  缺点:

  - Rebase 修改了提交树的历史

  比如, 提交 C1 可以被 rebase 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。

  一些开发人员喜欢保留提交历史，因此更偏爱 merge。而其他人（比如我自己）可能更喜欢干净的提交树，于是偏爱 rebase。仁者见仁，智者见智。

- 远程追踪

  Git 好像知道 `master` 与 `o/master` 是相关的。当然这些分支的名字是相似的，可能会让你觉得是依此将远程分支 master 和本地的 master 分支进行了关联。这种关联在以下两种情况下可以清楚地得到展示：

  - pull 操作时, 提交记录会被先下载到 o/master 上，之后再合并到本地的 master 分支。隐含的合并目标由这个关联确定的。
  - push 操作时, 我们把工作从 `master` 推到远程仓库中的 `master` 分支(同时会更新远程分支 `o/master`) 。这个推送的目的地也是由这种关联确定的！



​		直接了当地讲，`master` 和 `o/master` 的关联关系就是由分支的“remote tracking”属性决定的。`master` 被设定为跟踪 `o/master` —— 这意味着为 `master` 分支指定了推送的目的地以及拉取后合并的目标。

​		你可能想知道 `master` 分支上这个属性是怎么被设定的，你并没有用任何命令指定过这个属性呀！好吧, 当你克隆仓库的时候, Git 就自动帮你把这个属性设置好了。

​		当你克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 `o/master`）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 `master`。

​		克隆完成后，你会得到一个本地分支（如果没有这个本地分支的话，你的目录就是“空白”的），但是可以查看远程仓库中所有的分支（如果你好奇心很强的话）。这样做对于本地仓库和远程仓库来说，都是最佳选择。

​		这也解释了为什么会在克隆的时候会看到下面的输出：

```
	local branch "master" set to track remote branch "o/master"
```



##### 		我能自己指定这个属性吗？

​		当然可以啦！你可以让任意分支跟踪 `o/master`, 然后该分支会像 `master` 分支一样得到隐含的 push 目的地以及 merge 的目标。 这意味着你可以在分支 `totallyNotMaster` 上执行 `git push`，将工作推送到远程仓库的 `master` 分支上。

​			有两种方法设置这个属性，第一种就是通过远程分支检出一个新的分支，执行:

```
git checkout -b totallyNotMaster o/master
```

​			就可以创建一个名为 `totallyNotMaster` 的分支，它跟踪远程分支 `o/master`。

​	

我们检出一个名叫 `foo` 的新分支，让其跟踪远程仓库中的 `master`

```git checkout -b foo o/master; git pull```

正如你所看到的, 我们使用了隐含的目标 `o/master` 来更新 `foo` 分支。需要注意的是 master 并未被更新！



git push 同样适用

```git checkout -b foo o/master; git commit; git push```

我们将一个并不叫 `master` 的分支上的工作推送到了远程仓库中的 `master` 分支上



### 第二种方法

另一种设置远程追踪分支的方法就是使用：`git branch -u` 命令，执行：

```
git branch -u o/master foo
```

这样 `foo` 就会跟踪 `o/master` 了。如果当前就在 foo 分支上, 还可以省略 foo：

```
git branch -u o/master
```



跟之前一样, 但这个命令更明确！

- Git Push的参数1

  首先来看 `git push`。在远程跟踪课程中，你已经学到了 Git 是通过当前检出分支的属性来确定远程仓库以及要 push 的目的地的。这是未指定参数时的行为，我们可以为 push 指定参数，语法是：

  ```
  git push <remote> <place>
  ```

  `<place>` 参数是什么意思呢？我们稍后会深入其中的细节, 先看看例子, 这个命令是:

  ```
  git push origin master
  ```

  把这个命令翻译过来就是：

  **切到本地仓库中的“master”分支，获取所有的提交，再到远程仓库“origin”中找到“master”分支，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。**

  我们通过“place”参数来告诉 Git 提交记录来自于 master, 要推送到远程仓库中的 master。它实际就是要同步的两个仓库的位置。

  需要注意的是，因为我们通过指定参数告诉了 Git 所有它需要的信息, 所以它就忽略了我们所检出的分支的属性！  

- Git Push的参数2

  当为 git push 指定 place 参数为 `master` 时，我们同时指定了提交记录的来源和去向。

  你可能想问 —— 如果来源和去向分支的名称不同呢？比如你想把本地的 `foo` 分支推送到远程仓库中的 `bar` 分支。

  要同时为源和目的地指定 `<place>` 的话，只需要用冒号 `:` 将二者连起来就可以了：

  ```
  git push origin <source>:<destination>
  ```

  这个参数实际的值是个 refspec，“refspec” 是一个自造的词，意思是 Git 能识别的位置（比如分支 `foo` 或者 `HEAD~1`）

  一旦你指定了独立的来源和目的地，就可以组织出言简意赅的远程操作命令了



​	记住，`source` 可以是任何 Git 能识别的位置：

​	```git push origin foo^:master```

​	这是个另人困惑的命令，但是它确实是可以运行的 —— Git 将 `foo^` 解析为一个位置，上传所有未被包含到远	程仓库里 `master` 分支中的提交记录。

​	如果你要推送到的目的分支不存在会怎么样呢？没问题！Git 会在远程仓库中根据你提供的名称帮你创建这个分	支！

​	```git push origin master:newBranch```

​	很赞吧！它是不是很聪明？！





- Git Fetch的参数

  学习了 git push 的参数，很酷的 `<place>` 参数，还有用冒号分隔的 refspecs（`<source>:<destination>`）。 这些参数可以用于 `git fetch` 吗？

  你猜中了！`git fetch` 的参数和 `git push` 极其相似。他们的概念是相同的，只是方向相反罢了（因为现在你是下载，而非上传）

  ### `<place>` 参数

  如果你像如下命令这样为 git fetch 设置 <place> 的话：

  ```
  git fetch origin foo
  ```

  Git 会到远程仓库的 `foo` 分支上，然后获取所有本地不存在的提交，放到本地的 `o/foo` 上。

  

  通过指定 place...

  git fetch origin foo

  我们只下载了远程仓库中 `foo` 分支中的最新提交记录，并更新了 o/foo



​		“如果我们指定 `<source>:<destination>` 会发生什么呢？”

​		如果你觉得直接更新本地分支很爽，那你就用冒号分隔的 refspec 吧。不过，你不能在当前检出的分支上干这个事，但是其它分支是可以的。

​		这里有一点是需要注意的 —— `source` 现在指的是远程仓库中的位置，而 `<destination>` 才是要放置提交		的本地仓库的位置。它与 git push 刚好相反，这是可以讲的通的，因为我们在往相反的方向传送数据。

​		理论上虽然行的通，但开发人员很少这么做。我在这里介绍它主要是为了从概念上说明 `fetch` 和 `push` 的相似性，只是方向相反罢了。



​		来看个疯狂的例子：

​		```git fetch origin foo~1:bar```

​		哇! 看见了吧, Git 将 `foo~1` 解析成一个 origin 仓库的位置，然后将那些提交记录下载到了本地的 `bar` 分支		（一个本地分支）上。注意由于我们指定了目标分支，`foo` 和 `o/foo` 都没有被更新。

​		如果执行命令前目标分支不存在会怎样呢？我们看一下上个对话框中没有 bar 分支的情况。

​		```git fetch origin foo~1:bar```

​		看见了吧，跟 git push 一样，Git 会在 fetch 前自己创建立本地分支, 就像是 Git 在 push 时，如果远程仓库中		不存在目标分支，会自己在建立一样



​		没有参数呢?

​		如果 `git fetch` 没有参数，它会下载所有的提交记录到各个远程分支……

​		```git fetch```

​		相当简单，但是仅需更新一次，值得你去做！



- 没有source的source

  ​	古怪的 `<source>`

  Git 有两种关于 `<source>` 的用法是比较诡异的，即你可以在 git push 或 git fetch 时不指定任何 `source`，方法就是仅保留冒号和 destination 部分，source 部分留空。

  - `git push origin :side`
  - `git fetch origin :bugFix`

  我们分别来看一下这两条命令的作用……

  

  如果 push 空 <source> 到远程仓库会如何呢？它会删除远程仓库中的分支！

  ```git push origin :foo```

  就是这样子, 我们通过给 push 传空值 source，成功删除了远程仓库中的 `foo` 分支, 这真有意思...



​		如果 fetch 空 <source> 到本地，会在本地创建一个新分支。

​		```git fetch origin :bar```

​		很神奇吧！但无论怎么说, 这就是 Git！



- Git Pull 的参数

  因为 git pull 到头来就是 fetch 后跟 merge 的缩写。你可以理解为用同样的参数执行 git fetch，然后再 merge 你所抓取到的提交记录。

  还可以和其它更复杂的参数一起使用, 来看一些例子:



​		以下命令在 Git 中是等效的:

​		```git pull origin foo``` 相当于：

```
git fetch origin foo; git merge o/foo
```

还有...

`git pull origin bar~1:bugFix` 相当于：

```
git fetch origin bar~1:bugFix; git merge bugFix
```

看到了? git pull 实际上就是 fetch + merge 的缩写, git pull 唯一关注的是提交最终合并到哪里（也就是为 git fetch 所提供的 destination 参数）



如果我们指定要抓取的 place，所有的事情都会跟之前一样发生，只是增加了 merge 操作

```git pull origin master```

看到了吧! 通过指定 `master` 我们更新了 `o/master`。然后将 `o/master` merge 到我们的检出位置，**无论**我们当前检出的位置是哪



pull 也可以用 source:destination 吗? 当然喽, 看看吧:

```git pull origin master:foo```

哇, 这个命令做的事情真多。它先在本地创建了一个叫 `foo`的分支，从远程仓库中的 master 分支中下载提交记录，并合并到 `foo`，然后再 merge 到我们的当前检出的分支 `bar`上。操作够多的吧？！



#### 多用户配置

[     git-config的官网巨长文档](https://git-scm.com/docs/git-config)，开发人员经常遇到这样的问题，我们的公司仓库和个人仓库的用户名和邮箱配置是有区别的，为了能够很好地区分工程上传到不同的远程仓库，我们需要分别处理，保证在不同的工程使用不同的账户

1. 按工程配置多用户
2. 按目录配置多用户

##### 按工程配置多用户

​	目前git的配置变量可以放在三个地方：

1. /etc/gitconfig 系统配置,对所有用户都生效。

2. ~/.gitconfig 用户配置，仅对当前用户生效。

   ```
   git config --global user.name "yourName"
   git config --global user.email "yourEmail"
   ```

3. projectRootPath/.git/config 项目根目录配置，仅对当前项目生效。对应：进入工程根目录执行

   ```
   git config user.name "yourName"
   git config user.email "yourEmail"
   ```

三层是从3-2-1的优先级处理的，这样我们可以对不同工程完成不同的配置，这个在工程数量多的时候简直不忍直视，所以需要寻找更好的方法。

##### 按目录配置多用户

在2017年，git新发布的版本2.13.0包含了一个新的功能`includeIf`配置，可以把匹配的路径使用对应的配置用户名和邮箱；

在`~/`目录下面存在三个配置文件，

- .gitconfig // 全局通用配置文件
- .gitconfig-self // 个人工程配置文件
- .gitconfig-work // 公司工程配置文件

全局通用配置文件`~/.gitconfig`里面的内容是：主要是通过`includeIf`配置匹配不用的目录映射到不同配置文件上，

```
[includeIf "gitdir:~/self-workspace/"]
    path = .gitconfig-self
[includeIf "gitdir:~/workspace/"]
    path = .gitconfig-work
```

个人工程配置文件`~/.gitconfig-self`：

```
[user]
    name = yourname-self
    email = yourname-self@gmail.com
```

公司工程配置文件`~/.gitconfig-work`：

```
[user]
    name = yourname-work
    email = yourname-work@yourCompanyName.com
```

遇到的问题：

1. 文件`~/.gitconfig`里面的`includeIf`后面的path最后需要`/`结尾
2. 文件`~/.gitconfig`里面原有的user部分需要删除
3. 个人工程目录和公司工程目录需要要求是非包含关系，就是这两个工程目录配置路径不可以是父子关系。







# git push.default设置



转自：http://blog.csdn.net/daijingxin/article/details/51326715

 

在进行一次空仓库的提交时，我遇到了这个警告    警告如下`warning: push.defaul` 未设置，它的默认值将会在 Git 2.0 由 'matching'

修改为 'simple'。若要不再显示本信息并在其默认值改变后维持当前使用习惯，

进行如下设置：  git config --global push.default matching若要不再显示本信息并从现在开始采用新的使用习惯，设置：  git config --global push.default simple参见 'git help config' 并查找 'push.default' 以获取更多信息。（'simple' 模式由 Git 1.7.11 版本引入。如果您有时要使用老版本的 Git，为保持兼容，请用 'current' 代替 'simple' 模式）

```shell
No refs in common and none specified; doing nothing.Perhaps you should specify a branch such as 'master'.

fatal: The remote end hung up unexpectedly

Gogs: Internal errorFailed to execute git command: exit status 128error: 无法推送一些引用到 'git@softlab.sdut.edu.cn:daijingxin/addressbook.git'git push.default设置

October 31, 2013GNU/LinuxDebian, Gitopenwares   
```



​    当然首先我要先说明一下我电脑上git的版本，如下：

​    

```shell
$ git --version

  git version 1.8.4.rc3
```

 

​    之后我查找了一些关于git push.default设置的知识。

​    默认配置下，当使用git push命令而没有明确的指名本地分支和远程参考分支的情况下，会有如上的提示。

​    如果git push命令没有明确指定引用规格(refspec),也就是没有指定推送的源分支和目标分支，那么git会采用

push.default定义的动作。不同的值适用于不同的工作流程模式。



​    显而易见，主要是因为之前没有进行设置引用规格才出现的这种问题，现在我把push.default的可用值与配置方法贴在下面。



​    push.default可用的值如下：   

 1.nothing

​    不推送任何东西并有错误提示，除非明确指定分支引用规格。强制使用分支引用规格来避免可能潜在的错

误。



​    2.current

​    推送当前分支到接收端名字相同的分支。

​    

​    3.upstream

​    推送当前分支到上游@{upstream}。这个模式只适用于推送到与拉取数据相同的仓库，比如中央工作仓库流

程模式。

 

​    4.simple

​    在中央仓库工作流程模式下，拒绝推送到上游与本地分支名字不同的分支。也就是只有本地分支名和上游分

支名字一致才可以推送，

​    就算是推送到不是拉取数据的远程仓库，只要名字相同也是可以的。在GIT 2.0中，simple将会是push.default的默认值。

​    simple只会推送本地当前分支。

 

​    5.matching

​    推送本地仓库和远程仓库所有名字相同的分支。

​    这是git当前版本的缺省值。



​    一般来说我们使用simple就可以进行正常的使用，如果严格一点儿可以用nothing。

​    配置push.default的命令如下：

 

```shell
   git config --global push.default simple**
```

