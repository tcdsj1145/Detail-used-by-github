GIT总结使用

情景1：多人协作之如何建立本地分支？


假若你已经clone了别人的仓库并且需要修改，最好的办法是建立自己的分支然后在合并，具体步骤如下：


1.建立一个自己的分支

git branch mybranch

此时可以使用git branch查看当前的分支情况，如不 出意外，则显示一个master一个mybranchi分支，而你当前在master分支上。


2.切换到新建的分支

git checkout mybranch

此时我们就可以在myabranch随意的修改，这并不会影响刚才的主分支master。注意：以上全部操作都不影响远程仓库，都是本地的备份。


3.假若我们已经在mybranch上做了很多修改。那么我们就可以在mybranch下进行commit

git commit -m "i had do some thing in mybranch"


4.此时我们回到原来的master分支

git checkout master


5.将之前在branch上的修改合并到master

git merge mybranch


6.此时mybranch分支对我们来说已经没用了，删除

git branch -d mybranch


7.此时我们可以一次性将本地master的修改(merge后的)push到远程

git push


注意：如果想新建和切换branch一次完成，可以

git checkout -b branchname


情景2：多人协作之如何建立远程分支？


1.假如我和另一位同事一起操作一个rep(https://github.com/kkkkkxiaofei/trygit.git)，情景1对于我本地来说是很保险的，因为我本地新建的branch修改好了去merge本地的master而后才push到远程，但问题是如果我push的远程的还是有很多问题那就得在远程的master上去修改，其实也可以在远程上新建分支，全部push到这个新的分支上去。

git checkout -b shopping_cart

此时在本地新建并切换 到了shopping_cart分支。


2.将本地分支链接到远程分支

git push origin :shopping_cart

在操作这一步之前远程上是没有shopping_cart这个分支的，而现在有了。


3.对当前这个分支做些操作然后push

git add 2.html

git commit -m "add 2.html in shopping_cart branch"

git push

这三行全是在shopping_cart分支上操作的，远程上现在已经有对应的分支去被push，可以去github上看看在shopping_cart分支上确实多了一个2.html文件。


4.我觉得我的代码应该没问题，所以我要合并到主分支。

git checkout master

git merge shopping_cart

git push


5.现在轮到我另外一个同时操作了，他首先克隆可该rep然后查看分支

git clone https://github.com/kkkkkxiaofei/trygit.git

git branch

此时只显示了* master,为什么呢？因为对于这个同事来说，他的本地的确只有master分支。


6.查看远程分支

git branch -r

显示：

origin/HEAD -> origin/master
origin/master
origin/shopping_cart

这回他看到了我在远程上建立的shopping_cart分支。


7.他想操作shopping_cart分支

git checkout shopping_cart

此时checkout是把远程的shopping_cart放到了本地，并且切换到了该分支。

查看本地分支状态可以发现本地的确有了该分支：

   master
* shopping_cart

此时如果git remote show origin将会看到远程和本地同步了（都各自有两个分支）。


8.此时该同事查看了我在master上push的代码觉得没问题，那么远程的临时分支shopping_cart就没用了

git push origin :shopping_cart

显示：

To https://github.com/kkkkkxiaofei/trygit.git
 - [deleted]         shopping_cart

这条指令想必很熟悉吧，没错，建立和删除远程分支指令相同。执行指令，没有时就建立，就时就删除。


9.删除了远程分支，对于他而言本地的shopping_cart更没有用了，删除吧

git branch -d shopping_cart

如果报错试试 git branch -D shopping_cart

当然了，如果他觉得我的代码有问题，还可以在8.的时候修改自己的shopping_cart然后继续push到远程的shopping_cart。



情景3：撤销已git add的文件？


1.新建一个rep用以测试本例子，并且clone到本地

git clone https://github.com/kkkkkxiaofei/trygit.git


2.进入trygit文件夹下，该文件夹下只有一个README.md文件，手动修改该文件内容后查看diff

git diff

此时由于我修改了该文件，git显示差异如下：

diff --git a/README.md b/README.md
index 8686c6e..62a73fe 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,2 @@
-trygit
+trygit by zxf
 ======
很显然，我是将trygit改为了trygit by zxf


3.此时的README.md文件的状态肯定是unstaged的，可以查看

git status


4.我们现在把该文件stage一下

git add README.md


5.现在查看修改的差异

git diff

发现没有任何内容，为什么呢?因为git diff是检测unstaged文件的差异。


6.现在查看staged的差异

git diff --staged

这时就输出了和2.一样的结果。此时文件的状态是changes to be committed.


7.哦，糟糕了，我想git add的文件并不是README.md，肿么办？

git reset HEAD README.md

此时出现如下提示：

Unstaged changes after reset:
M README.md

说明我们重置了HEAD指针后，README.md变为了unstaged.当然了，现在又可以用git diff了。


8.既然我都git add了错误的文件，那是不是之前的修改也要撤销呢？

git checkout -- READM.md

此时出现：

On branch master
Your branch is up-to-date with 'origin/master'.


nothing to commit, working directory clean

意思太明显了，该文件又回到了以前的原始状态(内容为trygit)，利用checkout可以将一个已经track的file变为untrack.

ps：什么是track呢？只要在当前git工作目录下文件是untrack的，然后利用git add就可以将件变为staged的，而当该文件最终的修改被push后，它就会回到原来的状态，这个原来的状态并不是指它刚建立的时候，那个时候它是untrack，而现在它被push后是变成了track状态了。举个最简单的例子，你新建的文件无论如何修改，你都看不到git diff，但当你把他git add 后，这时git 就可以track到了。


情景4：撤销已经commit的文件？


1.修改1.html然后提交（注意默认该文件已经是track状态）

git commit -a -m "add 1.html file"

-a可以捕捉所有已tack的文件的changes，这样就省去了git add xxx1 xxx2 xxx3的繁琐。


2.哦，糟糕了，我没有把我修改的内容注释写在提交里，我想补充，肿么办？

git reset --soft HEAD^

该指令会将commit状态转变为staging状态，此时git status：

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)


modified:   1.html

提示你有修改，可以commit的，那太明显了，这就是staging状态啊。


3.重新修改commit

git commit --amend -m "add 1.html file and modify its content"

这时git log可以看到只有后面这条commit记录，之前的没了。

注意，在这个情景中，其实如若只是把commit的注释写错了，直接使用3.即可，而你如果不单单是修改注释这么简单，还想git add其他文件作为这次commit，那么就需要2.

其他相关指令：

撤销上一次的commit和all changes

git reset --hard HEAD^

撤销上二次的commit和all changes

git reset --hard HEAD^^

情景4：rebase

git checkout mybranch

git rebase master

git checkout master

git merge mybranch

git fetch






