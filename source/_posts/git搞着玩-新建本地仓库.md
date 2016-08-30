---
title: git搞着玩-新建本地仓库与分支合并
date: 2016-08-19 11:01:47
categories: git
tags: git
---
用了git有一段时间了，在项目团队刚切入git时还是遇到了不少的阻力，各种质疑，为什么svn用的好好的突然转到git上面？为什么git操作这么复杂？等等等等；当然我作为逗比的我还是很愿意为他们一一诉说我对git的真爱的。当然并不是说svn不好，我可以说svn非常好，尤其某些场景更是强大至极，至少对于小团队或者单产品线研发模式来讲用svn是非常好的选择。我们之所以从svn平台切换到git上面主要是因为我们的研发路线是一个主产品线+N个项目定制线；并且主产品线会和其中的项目定制线进行彼此合并操作；

再举个切实的例子，张三丰正在开发太极拳产品，并且已经发布了1.0版本，投入市场后非常受欢迎，当然也收集了一些用户的反馈，经过研发部讨论可以把一些建议并入主产品开发，另外基于主产品线再开出一个branch做另一个小众市场的试错项目，这项目由张无忌开发；那么问题来了，以后类似这样的项目会越来越多，程序员一人多用的现象已经中国这片土地上面成为常态，所以程序员们也必然会通过自身努力练就三头六臂的本领；所以张无忌现在会同时(犹如电脑CPU进程切换一样)负责3个项目；那么比如张无忌正在开发A项目，然后B项目突然反馈一个致命bug，必须立刻马上解决；后来这个bug发现C项目也有；那么张无忌是不是先把正在开发的A项目的代码提交(不能提交到远程代码仓库，因为开发的A项目代码还是半成品)，然后去修改B项目的致命bug；这时如果用的svn的话，具体咋搞？这里我就不具体说了，反正我觉得张无忌会哭！那么用git就非常爽啦，针对项目A分支进行add、commit就行了；然后checkout B，切换到B项目分支上改bug就行了，改完后合并到C项目上就行啦！你说爽不爽，这就是中央仓库和“分布式”仓库的巨大差别！看到其它同事茫然的点点头，我心中窃喜！

扯的有点远了，回到正题上来！git不就是个版本管理工具嘛，有什么了不起的，先自己随便搞搞；用一段话描述项目的需求如下：在本地新建一个git代码仓库，然后添加一个分支develop，在分支develop中添加一个文件，然后再添加一个分支master，将develop分支中的文件合并到master分支中，然后同时修改develop分支中的文件和master分支中的文件并合并，遇到冲突后解决冲突，就这么多。

**一、新建本地仓库（git init）**
```java
	$ git init
	Initialized empty Git repository in D:/workspace/gitDemo/.git/
```
提示初始化一个空的git仓库在XXXX目录中

**二、新建develop分支（git branch develop）**
*- 先检查刚才新建的本地空仓库中是否有分支（git branch）;明显没有嘛，这还用想？不试试怎么知道，好吧就试试吧！*
```java
	$ git branch
	
```
输入命令后什么都没有输出，表名该仓库还没有任何分支；当然git branch命令会列出本地仓库的所有分支，并会标示出当前所在的分支。

*- 新建develop分支操作（git branch develop）*
```java
	$ git branch develop
	fatal: Not a valid object name: 'master'.
	
```
我去，直接给我亮出了红牌的致命警告，真是道路曲折啊，没法儿玩儿了；出现上面的提示是因为根据git约定要求，在空仓库中新建分支之前，需要commit一一下，好吧！那照做呗....

*- 提交一下（git commit）*
```java
	$ git commit -m "按照git要求，执行一次空的提交操作"
	On branch master

	Initial commit

	nothing to commit

```
哎呀我去，我只是提交了一下啊，它怎么做了这么多事情(三行英文的意思就是 当前分支在master上面、初始化提交、提交的内容为空)；后两句我可以理解，那么当前分支为master这句话我有点理解不了。

出现上面的提示(On branch master)是因为git仓库默认初始分支是master；而你直接在空的仓库中提交时，会坚持当前仓库是否有master分支，如果没有，则新建master分支，好吧，我只能说它太“人性化了”。那么其实到这里我们已经通过commit操作创建了git默认的master分支了。

*- 看看刚才git自动为我新建的master分支真的创建完了吗*
```java
	$ git branch
	
```
哎呀，我去，怎么还显示空啊，难道创建失败了？好吧，在告诉你一个秘密：其实master分支已经自动创建完成了，只是不显示而已，你需要添加（add）一个物理文件进去，然后提交(commit)就可以看到master分支了。
```java
	$ echo "hello maps" >index.txt

	$ git commit -m "commit index.txt file"
	[master (root-commit) be4ae99] commit index.txt file
 	1 file changed, 1 insertion(+)
 	create mode 100644 index.txt

	$ git branch
	* master
```
通过上面三步(添加一个物理文件、add到git的index库中、commit到git的本地仓库中)之后，我们可以看到master分支已经显示出来了！针对不易啊！
*注：其实上上步骤(上一步的上一步)中我们描述的执行一次空的提交(commit)是多余的；完全用上面三步走战略就行啦！*

通过第二大步的操作后，我们本来新建develop分支的，结构却不由自主的“被创建一个master分支”。现状的状况就是我们在空的仓库中新建了一个master分支，然后在master分支中新建了一个index.txt文件。下面只能操作第三步，再次新建我们的develop分支喽。


**三、再次新建develop分支（git branch develop）**
```java
	$ git branch develop
	
```
执行完新建develop命令后，我们查看develop命令是否新建完成了（怎么现在对git branch这个命令越来越怀疑了，不会它又自动为我新建一个其它的分支吧，哈哈。眼见为实，操作看看吧）
```java
	$ git branch
  	develop
	* master
	
```
看到了吧，我们新建的develop分支已经出来了，但是奇怪的是我们上一步新建的master分支前面为什么一个"*"呢，其实这个"*"代表当前所在的分支；上面的意思就目前仓库中有两个分支(develop、master)；而你当前所在的分支是master中。*那么到目前为止，现在的状况就是在本地代码仓库中，有一个master分支和一个develop分支，而master分支中有一个index.txt文件，而develop中没有任何文件。*

**四、把master分支中的文件合并到develop中**

- 如标题一样，我们的目的是把master分支中的文件合并到develop中去，但是我们还不能直接用合并命令，还需要按照步骤执行如下操作：

1、切换到develop分支中(为啥这么做呢，git的合并规则是这样，比如我要把master分支合并到develop分支中，那么我就需要先切换到develop分支中，然后执行合并命令)

   先切换到develop分支中
	```java
		$ git checkout develop
		Switched to branch 'develop'
	
	```
   切换完成后，看看我们当前所处的分支是否是develop分支（看到没，develop前面一个“*”，代表当前所在分支是develop）
	```java
		$ git branch
	  * develop
	  	master
	
	```

2、执行合并分支的命令(*所以git合并命令git merge master的真正意思就是把master分支合并到我所在的当前分支develop中*)

   然后执行合并master分支到develop分支中的操作(执行完成后，我们就可以到develop分支下面看到合并过来的index.txt文件了)
	```java
		$ git merge master
		Already up-to-date.
	
	```

**五、修改合并到develop分支中的文件内容为hello develop，并提交**

我们因为执行了上一步的合并master分支到develop分支的操作，所以我们当前所在分支是develop；那么我们修改该分支下面的index.txt文件内容为：hello develop
```java
	原内容：hello maps
	新内容：hello develop
	
```
然后执行添加(add)和提交(commit)操作.
```java
	$ git add index.txt
	
```
```java
	$ git commit -m "修改index文件为hello develop"	
	[develop bf5d39d] 修改index文件为hello develop
 	1 file changed, 1 insertion(+), 1 deletion(-)

```

**六、切换到master分支,并修改文件内容为hello master**

   切换到master分支
```java
	$ git checkout master
	Switched to branch 'master'	

```

   修改文件内容(因为上一步修改的是develop分支中的index文件，那么我们切换到master分支后，其中的index.txt文件是不会发生改变的，所以内容依然是hello maps)
```java
	原内容：hello maps
	新内容：hello master
	
```
   
**七、小述一下**
	
到目前为止，我们修改了develop分支中的内容为：hello develop；而修改master分支中的内容为：hello master；并且当前所在分支是master分支，那么接下来我们需要合并develop分支中的文件到当前所在分支master中来。因为两个分支中的文件都发生了更改，所以合并时肯定会报冲突，至于冲突下面解决。

**八、合并develop分支的内容到master分支中**
 
- 先检查一下当前所在分支是否是master中（看到了吧 ，master分支前面一个“*”号，代表当前所在分支是master上面）

```java
	$ git branch
  	develop
  * master

```

- 合并develop分支中的内容到master分支上面（我们执行合并命令后git merge develop之后提示了4行信息）
```java
	$ git merge develop
	Auto-merging index.txt
	CONFLICT (content): Merge conflict in index.txt
	Automatic merge failed; fix conflicts and then commit the result.

```
上面的提示证明此次合并失败，以为两个文件中的内容合并过程中冲突了。

- 使用git diff命令查看两个文件冲突的内容
```java
	$ git diff
	diff --cc index.txt
	index 2e6baf7,246b739..0000000
	--- a/index.txt
	+++ b/index.txt
	@@@ -1,1 -1,1 +1,5 @@@
	- hello master
	 -hello develop
	++<<<<<<< HEAD
	++hello master
	++=======
	++hello develop
	++>>>>>>> develop
		
```

- 我们也可以到当前分支master中直接打开index.txt文件查看，或者用cat命令查看也可以
```java
	$ cat index.txt
	<<<<<<< HEAD
	hello master
	=======
	hello develop
	>>>>>>> develop

```
**九、冲突解决--手工**
	
我们执行合并操作后，因为两个分支分别对同一个文件进行了修改，所以合并的时候就报冲突了(其实合并执行后，git把文件中凡是冲突的地方都写到当前分支的文件中了，只不过用特殊标记标注了而已)下面是合并冲突后的index.txt文件内容

```java
	$ cat index.txt
	<<<<<<< HEAD
	hello master
	=======
	hello develop
	>>>>>>> develop

```
针对上面的文件，我们先看<<<<<<<（7个<） 到 =======（7个=）之间的内容为：hello master；这就是当前所在分支master
分支下面index.txt的文件内容(一般是我修改的)；再接着看从=======（7个=） 到 >>>>>>>（7个>）之间的内容为：hello develop；这就是develop分支下面index.txt的文件内容(一般是别人修改的)。

所以我们现在要做的就是解决冲突，具体怎么解决冲突，有以下处理办法：

 1、把develop分支中的内容覆盖(擦除)掉master分支中的内容(就好比develop战胜)

 	文件最终内容为：hello develop

 2、把master分支中的内容覆盖(擦除)掉develop分支中的内容(就好比master战胜)

   文件最终内容为：hello master

 3、把master分支中的内容与develop分支中的内容都保留(就好比develop分支和master分支和解)

   文件最终内容为：hello master hello develop

不管最终我们以何种方式解决冲突(以谁的内容为主),最终我们都需要删除index.txt文件中的其他无用的git冲突标示符("+"、"<"、"="、">")；然后我们执行添加(add)和提交(commit)操作,具体不在详述了，前面有说到！


**十、冲突解决--工具**

第九步中，我们已经通过手工方式解决了冲突，但是如果一个文件中有N多地方的内容都冲突，那么我们通过手工解决非常费劲，并且容易出错，所以借助工具(mergetool)解决这些体力活是明智之举。

```java
	$ git mergetool

	This message is displayed because 'merge.tool' is not configured.
	See 'git mergetool --tool-help' or 'git help config' for more details.
	'git mergetool' will now attempt to use one of the following tools:
	opendiff kdiff3 tkdiff xxdiff meld tortoisemerge gvimdiff diffuse diffmerge ecmerge p4merge araxis bc codecompare emerge vimdiff
	Merging:
	index.txt
	
	Normal merge conflict for 'index.txt':
	  {local}: modified file
	  {remote}: modified file
	Hit return to start merge resolution tool (tortoisemerge):

```
输入“git mergetool”命令后，直接再按一次回车键就会弹出合并工具的界面：
![Alt text](http://soujava.com/images/gitmerge1.png "git 合并工具")

你可以右键点击冲突的行内容，然后点击弹出的菜单项进行合并操作，然后可以看工具的下面区域实时显示合并后的内容效果。

通过工具合并后，我们仍然需要直接添加(add)和提交(commit)操作。