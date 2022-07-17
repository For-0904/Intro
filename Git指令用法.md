# Git

Git是目前世界上最先进的分布式版本控制系统：

![763193-322be218cf2b6d7c](https://user-images.githubusercontent.com/75195605/179390282-85c78e04-5db1-41e6-bab7-6c5d32191545.png)


它可以用于个人或者团队的开发工作，保存每一次开发新feature的版本信息，帮助代码的管理、调用和开发。

以下将从repo的创建，某一个feature的开发讲起，以实际例子学习git。

## 存储原理

![763193-edbc86ee8ec8a72f](https://user-images.githubusercontent.com/75195605/179390295-08c15707-97d5-4674-a8ff-f120c4da0d23.png)
- **工作区**（Working Directory）:电脑里能看到的目录；
- **版本库**（Repository）:工作区中有一个隐藏目录`.git`时，为Git的版本库

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向master的一个指针叫`HEAD`。

因此在本地，我们有自己的文件夹（⚠️：是从repo `git clone`下来的），我们可以对代码进行修改、添加和删除；本地修改后，并不会影响版本库中的代码，如果想要把自己的修改在repo中体现出来，我们接下来需要`git add`操作，将需要提交修改的文件添加到stage中，也就是一个缓冲区（⚠️：此时版本库还没有更新）；最后，我们`git commit`之后，`git push`上去就可以了。

## 开发过程

现在，你进入一个项目组，该project的代码是通过github进行维护的，在github上有一个叫做Secret的repository。你的boss提出了一个新的需求（新feature），需要你完成参与开发，接下来就讲解你将会遇到的各种git应用场景和问题：

### 1. 将远端repo代码拉取至本地

需要在本地开发，使用`clone`将远端repo拉取到本地：

```shell
git clone <url>
```

- url：repo的url

<img width="539" alt="截屏2022-07-17 16 26 56" src="https://user-images.githubusercontent.com/75195605/179390313-7ebf6338-a207-46d6-add2-eb43cbed2807.png">

这样本地就有对应的代码了。

### 2. 更新本地代码

有可能repo的维护者修改了整个代码的结构，比如将一些代码整理到同一个文件夹下，此时如果你修改的正是远端已经修改的文件，那么git会报错：conflict。为了避免这种情况，使用`pull`拉取最新的内容到本地，进行更新：

```shell
git pull origin master
```

**⚠️：origin是master主分支的名字！**

### 3. 开始开发，创建分支

在你开始开发新的feature时，强烈建议创建一个自己的branch。一开始只有master分支，后面随着开发人员增多，一个主分支肯定不好管理，可能会带来代码错误，如何解决这个问题？当然是得有人来进行审核，同时进行测试，这些都需要一个叫做——Pull Request的功能来实现。

首先需要创建一个自己的分支，在上面进行代码修改和功能开发，使用`branch`进行创建：

```shell
git branch <name>
git checkout <name> # 转换到当前分支
```

- name：新建branch的名字

在该分支下，就可以进行开发了，不用担心会破坏repo代码，因为此时你还没有进行任何merge的操作！

### 4. 初步代码形成，进行PR

在当前branch已经完成初步开发之后，同时进行了基本的功能测试，为了能够完成boss需求，下一步很重要——进行PR。

首先，通过`status`查看在此branch上做了哪些代码的修改：

```shell
git status
```

如果有修改、增加或者删除，会分别显示modified、untrack、deleted。

之后，就需要进行提交操作，将修改的文件先加入缓冲区里面，使用`add`：

```shell
git add <files>
```

- files：文件，可以多个，用空格隔开

在缓冲区之后，如果确认无误，那么就进行下一步提交到版本库中：

```shell
git commit -m <note>
git push origin master # push到repo中
```

- note：这次提交的名字

这样，你就可以在github界面上看到自己的提交了，同时能够看到有哪些文件、文件中的哪些代码被修改了，方便检查。

第二种情况是如果误加了一些文件到缓冲区，那么使用`checkout --`完成撤销：

```shell
git checkout -- <file>
```

- file：待撤销的文件

自此，你已经可以在repo上看到你的提交，如果无误后，便可以点击create PR。

这一步，会将你的修改作为pr提交上去，是public的，也就是其他人都可以看。相关人员会对代码进行评价，完成code review之后，则需要进行测试，如果测试也通过，那么最后就可以merge到master主分支上，你的开发也就完成了。

### 5. 撤销

现在，出现了问题，在做code review的时候，发现你的代码从很早的一次提交开始就有问题，但是现在由于feature功能复杂，你已经记不到是在哪儿出了问题，从那次之后的开发都是有问题的，此时怎么办呢？回退是一个很强大的功能。

我们的当前节点都是通过HEAD指针指向的，因此回退的实质其实就是HEAD指针的移动。

首先，通过`log`查看历史提交记录，或者通过`reflog`查看命令历史：

```shell
git log
git reflog
```

此时，会显示所有的提交历史，包括其他人的，那么你只需要查看自己的历史，明确需要回退到什么地方：

- 通过commit id指明；
- 通过HEAD、HEAD^（上上版本）、HEAD^^（上上上版本）等；

确定之后，通过`reset`命令完成回退：

```shell
git reset --hard <版本号>
```

- 版本号：commit id 或者 HEAD等；

### 6. 撤销+

还有一种更加普遍的情况是，如果你已经将修改的代码push上去了，但是现在你想删除几个文件，那么你可以这样做：

- 首先，预览我们要删除的文件或者文件夹

  ```shell
  git rm -r -n --cached 文件/文件夹
  ```

- 其次，确认无误之后，就可以删除这个文件或者文件夹了，只需要去掉-n参数

  ```shell
  git rm -r --cached 文件/文件夹
  ```

- 接下来，就是正常操作，提交到远程

  ```shell
  git commit -m "delete xxx file"
  
  git push origin <branch>
  ```

## 可能遇到的问题

### 1. 删除分支

如果有需求要删除分支，那么可以通过`branch`来完成：

```shell
git branch -d <name>
```

- name：待删除的分支名

**⚠️：如果当前分支为需要删除的分支，那么是无法删除成功的。**

### 2. 冲突解决

如果在当前有两个提交，并且都修改的同一个位置，那么会产生冲突，系统会提示需要手动进行修改：

```shell
<<<<<<< feature1
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature2
```

上面就显示了冲突的位置，手动修改后提交，即可。

### 3. 查看文件的不同

在进行提交的时候，如果不清楚该文件修改了哪些地方，那么可以通过`diff`来完成：

```shell
git diff <file>
```

- file：查看修改文件的路径
