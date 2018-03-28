# How Git Works

## Git Is Not What You Think

### Meet SHA1

* Git is a map of keys and values
* Value are objects and keys are SHA-1 hash

* use `git hash-object filename` to generate a hash key for a file. The following example generates a hash key for a text

```shell
$ echo "Apple Pie" | git hash-object --stdin
bb3918d5053fea31fc9a58fae1e5bdeabe3ec647
```

### Storing Things

* you can create a new git repository using `git init`:

```shell
$ git init
Initialized empty Git repository in C:/Project/.git/
```

* The repository is stored in the .git directory

* the following command creates the directory and the blob file for the text

```shell
$ echo "Apple Pie" | git hash-object -w --stdin
bb3918d5053fea31fc9a58fae1e5bdeabe3ec647
```

* Blob files for objects added to the repository are stored in a directory inside object directory. the directory's name starts with the first two characters of the returned hash key (e.g. in the above example)
* The name of the blob file is the rest of the hash key (i.e. remove the first two characters)

* git status displays the state of the working directory and the staging area

```shell
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   menu.txt
        new file:   recipes/README.txt
        new file:   recipes/apple_pie.txt
```

### First Commit!

* To commit a new file we need to first add them to the staging area
* The staging area is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the “index”, but the phrase “staging area” works just as well.

* use `add` to add changes to the staging area:

```shell
$ git add menu.txt               # the chagne here is that we have a new file
```

* `commit` records changes to the repository. It stores the current contents of the index in a new commit along with a log message from the user describing the changes.

```shell
$ git commit -m "My Message!"
[master (root-commit) c3c8b07] First Commit!
 3 files changed, 2 insertions(+)
 create mode 100644 menu.txt
 create mode 100644 recipes/README.txt
 create mode 100644 recipes/apple_pie.txt
```

* use `git log` to look at the list of the existing commits

```shell
commit 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c (HEAD -> master)
Author: Doe <john.doe@example.com>
Date:   Fri Mar 23 12:06:55 2018 -0500

    First Commit!
```

* Use `cat-file` to check the content of an object (e.g. commit, tree, blob, etc.) in the object folder (-t: type. -p: print)

```shell
$ git cat-file -p 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c
tree 127ea67bfaa06ad1407ff08bcbadea12e531568c
author Doe <john.doe@example.com> 1521824815 -0500
committer Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521824815 -0500

First Commit!
```

* tree in the above example is the root directory

```shell
$ git cat-file -p 127ea67bfaa06ad1407ff08bcbadea12e531568c
100644 blob 9eed377bbdeb4aa5d14f8df9cd50fed042f41023    menu.txt
040000 tree 184c2d41e294290bcaa65d0d0960e8929a210f36    recipes
```

* the numbers at the beginning of the line in the above example are access permissions
* a blob is not really a file it is the content of a file. The file name and the file permissions are not stored in the blob. They are stored in the tree that points to the blob (you will see later why this is a good thing)
* the following image show the object database. The content of the `menu.txt` and `apple_pie.txt` are the same so root and recipes trees both point to the same blob:

![The Object Database](https://github.com/farbod2015/javascript/blob/master/img/objectdb1.jpg)

### Versioning Made Easy

* if we edit the `menu.txt` and add a new line "Cheesecake" and commit. The content of the new commit will look like this:

```shell
$ git commit -m "Add Cake"
tree f6339016a5a44313c05b9e2831c4edb324a69548
parent 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c
author Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521834006 -0500
committer Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521834006 -0500

Add Cake
```

* Commits are linked. In the above example the parent is the first commit.
* The tree of the second commit is different than the tree of the first commit.
* if we check the content of the tree of the second commit it will look like this:

```shell
$ git cat-file -p f6339016a5a44313c05b9e2831c4edb324a69548
100644 blob b63cba247afc716104ab997704f0973823894907    menu.txt
040000 tree 184c2d41e294290bcaa65d0d0960e8929a210f36    recipes
```

* the blob for the `menu.txt` in the second commit is different than the blob in the first commit since its content has changed but the content of the `recipes` tree has not change so the hash key remains the same
* Here is the file structure of the object database after the second commit:

![The Object Database](https://github.com/farbod2015/javascript/blob/master/img/objectdb2.jpg)

* As you can see we have currently 8 objects in the database. We can check the number of objects using `count-objects`:

```shell
$ git count-objects
8 objects, 32 kilobytes
```

* for huge files git maybe store the difference between the two version and it may compress multiple objects into a single blob.

### One More Thing: Annotated Tags

* There is another type of objects in git called tags. A tag is like a label for the current state of the project (i.e. a simple label for an object). There are two types of tags in git _regular tags_ and _annotated tags_.
* Annotated tags are the ones that come with a message. To create an annotated tag, you could use the git tag command with the -a argument, and you need a name for the tag and a message:

```shell
$ git tag -a mytag -m "I love cheesecake"
```

* you can get a list of the tags using `git tag` command
* Annotated tags are also objects like commit. You cat-file tags using their hash key or tag name:

```shell
$ git cat-file -p mytag
object f4d96566d0a09558fd04a52fb07e407c9d6e97bd
type commit
tag mytag
tagger Doe <john.doe@example.com> 1521835840 -0500

I love cheesecake
```

* In the above example, the object that the tag is pointing to is a commit
* Here is how the object database look after adding the tag:

![The Object Database](https://github.com/farbod2015/javascript/blob/master/img/objectdb3.jpg)

### Recap

In the Git object database you have:

* Blob (arbitrary content)
* Trees (the directories)
* Commits
* Annotated Tags

## Branches Demystified

### What Branches Really Are

* you can check the list of the branches with `git branch`
* Git normally puts branches in `refs/heads` directory.
* Right now the `heads` folder only contains `master`. It is not a compressed file (unlike blobs) and its content is only a hash key for the current commit.

```shell
$ cat .git/refs/heads/master
f4d96566d0a09558fd04a52fb07e407c9d6e97bd
```

* so we have two linked commits in this project, and we also have a master branch which is just a simple reference (pointer) to a commit (in this case last commit) and that is why the directory is called `refs`:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branch1.jpg)

* now lets create a new branch called lisa that contains alternative recipes:

```shell
$ git branch lisa
```

* the new branch is pointing to same commit that master branch is pointing to:

```shell
$ cat .git/refs/heads/lisa
f4d96566d0a09558fd04a52fb07e407c9d6e97bd
```

* This is what we have now two commits and two branches, and the branches are pointing at the same commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branch2.jpg)

### The Mechanics of the Current Branch

* the current branch is marked with * on the list of branches:

```shell
$ git branch
  lisa
* master
```

* the current branch is stored in `.git/HEAD`. `HEAD` is a reference to a branch or a pointer to a pointer:

```shell
$ cat .git/HEAD
ref: refs/heads/master
```

* If we change `apple_pie.txt` and commit the new changes then the master branch will point to the new commit and the head is still pointing to the master branch:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 cup sugar
8 Granny Smith apples
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/newcommit1.jpg)

* use `checkout` to change the current branch:

```shell
$ git checkout lisa
Switched to branch 'lisa'
```

* two things happen when we do a `checkout`:
    1. `HEAD` now points to `lisa`
    1. our working area changes to the content of the commit pointed at by `lisa`

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/checkout1.jpg)

* so in short `checkout` means move `HEAD` and change the working area

* lets edit the `apple_pie.txt` recipe in lisa's branch:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 cup sugar
1 tbsp cinnamon
10 Granny Smith apples
```

* if we commit the chages, Git will add the commit to the object database and move the current branch, lisa, to point at the new commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branching1.jpg)

* **Note:** branches are just references to commits.

### Let's Merge!

* now if we do `git checkout master` the `HEAD` will point to the master branch:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/checkout2.jpg)

* we are going to have a conflict, if we merge lisa's changes from her branch into the master branch:

```shell
$ git merge lisa
Auto-merging recipes/apple_pie.txt
CONFLICT (content): Merge conflict in recipes/apple_pie.txt
Automatic merge failed; fix conflicts and then commit the result.
```

* Here is our `apple_pie.txt` after the conflict:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 cup sugar
<<<<<<< HEAD
8 Granny Smith apples
=======
1 tbsp cinnamon
10 Granny Smith apples
>>>>>>> lisa
```

* we need to solve the confilct manually:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 cup sugar
1 tbsp cinnamon
9 Granny Smith apples
```

```shell
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   recipes/apple_pie.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

* to tell Git that the conflict has been fixed, we need to stage the changes and then commit. The commit does not need a message in this case since it will open vim automatically to edit the message that it has generated (hit `Esc` and then `:wq` to save and quit):

```shell
$ git add .\recipes\apple_pie.txt
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

        modified:   recipes/apple_pie.txt
```

```shell
$ git commit
[master db6e168] Merge branch 'lisa'
```

* `merge` is a commit and if we look inside this commit using `cat-file` we can see that it has two parents:

```shell
tree 4de2fe2bc8400d05e060681133fd3db89712e015
parent 64fd38d40288b792ce1ae3ffaeb27912052900c9
parent 00a343be57c923c9ebcf7a900454cd1210d598cf
author Farbod <farbod@example.com> 1522080262 -0500
committer Farbod <farbod@example.com> 1522080262 -0500

Merge branch 'lisa'
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/merging1.jpg)

### Time Travel for Developers

* so far we have learned that the objects in the database are commits, trees, blobs, and tags; and all these objects are arranged in a graph and they reference each other. The references are: from a commit to its parent, from a commit to its tree, and from trees to blobs

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/history1.jpg)

* the references are used in two ways:
    1. references between commits are used to track history.
    1. all other references are used to track content.

* **Note:** you can have objects that are reachable from more than one commit which makes it possible to reuse contents

* when we do a checkout, git doesn't care about history and the way that commits are connected to each other. It just cares about trees and blobs that can be reached from that commit which is the entire state of the project at the time of the commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/history2.jpg)

* Git uses this information to replace the content of the working directory (this is how we travel in time in Git)

### Merging Without Merging

* now if we want to meger the master branch with lisa so that lisa would be up do date as well, we need to do a `git checkout lisa` and then `git merge master`.

* Since we have resolved the conflict when we were merging it the other way (merging lisa into the master branch), git will not ask us to resolve the conflict and also it is not going to create a new commit that is a child of lisa and the master branch because master branch itself is the result of the merge. So it will only point lisa to the master branch as the result of the merge:

```shell
$ git checkout lisa
Switched to branch 'lisa'
```

```shell
$ git merge master
Updating 00a343b..db6e168
Fast-forward
 recipes/apple_pie.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/merging2.jpg)

* **Note:** This is called _fast-forward_ merge.

* **Note:** Here are the definitions of the two types of the merge:

  * If master has diverged since the feature branch was created, then merging the feature branch into master will create a merge commit. This is a _typical merge_:

    ![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/merging3.jpg)

  * If master has not diverged, instead of creating a new commit, git will just point master to the latest commit of the feature branch. This is a _fast forward_:

    ![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/merging4.jpg)

  **Note:** Passing `--no-ff` creates a new commit to represent the merge, even if git would normally fast forward:

    ![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/merging5.jpg)

### Losing Your HEAD

* you can checkout a commit just like you can checkout a branch. In this case, HEAD will point to the commit instead of a branch:

```shell
$ git checkout db6e1684
Note: checking out 'db6e1684'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at db6e168... Merge branch 'lisa'
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/checking3.jpg)

* this situation is called _detached HEAD_, because we don't have a current branch in this state:

```shell
$ git branch
* (HEAD detached at db6e168)
  lisa
  master
```

* now lets make changes to `apple_pie.txt` and do a commit:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 cup sugar
1 tbsp cinnamon
20 Granny Smith apples
```

* in this case HEAD is not pointing to a branch so it will move with the commit directly. It is working exactly like branch here:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/detached1.jpg)

* lets make more changes to `apple_pie.txt` and commit again:

```txt
Apple Pie

pre-made pastry
1/2 cup butter
3 tablespoons flour
1 tbsp cinnamon
20 Granny Smith apples
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/detached2.jpg)

* now if we checkout the master branch the two previous commits are still in the object database, but they are now unreachable and isolated unless we know their SHA-1s:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/detached3.jpg)

* objects that can't be reached by any reference get garbage collected. Evey now and then Git decides that it is time for running a garbage collection. The garbage collector will look for objects in the database that cannot be ulimately reached from a branch, HEAD, or a tag and it will remove them to save disk space.

* as mentioned before we can still save those commits using their SHA-1s by checking out the commit and then create a branch for it (e.g. nogood). These objects will never be garbage collected:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/detached4.jpg)

* This is a common way to use a _detached HEAD_: when you want to try out something, go down maybe and run a general experiment with your code, you can just detach HEAD, do your experiment, still commit the experiment as much as you wish so that you won't lose data, and then you decide whether to keep the experiment or to do away with it. Just remember to put a branch on the stuff that you care about before you leave it behind.

### Objects and References

* we can summarize what we discussed in this section in three rules:
  1. The current branch tracks new commits
  1. When you move to another commit, Git updates your working directory
  1. Unreachable objects are garbage collected

## Rebasing Made Simple

### What a Rebase Looks Like

lets say we have a new branch for a new recipe and we alaready have some commits on that branch:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase1.jpg)

now we want to put the content of the two branches together. We already know one way to do this which is merging the two branches and this is how it would look like:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase2.jpg)

but we are going to use the second way and that is _rebase_. In the following image the base of the spaghetti branch is shown in red color. All the history before this commit is already shared between the two branches:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase3.jpg)

 To do a rebase, Git detaches the entire spaghetti branch from this commit and moves it to the top of the master so it changes the base of the spaghetti branch to the commit that the master branch is pointing to. Thast is why it is called rebase:

```shell
$ git checkout spaghetti
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: Add spaghetti alla carbonara
Applying: Add carbonara ingredients
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase4.jpg)

Just like merge we might have to solve a conflict for rebase. Now the spaghetti branch has all the stuff from the master branch but if we want it to work the other way as well and we want the stuff from spaghetti in the master branch, we can just do like merge and checkout the master branch and rebase the other way:

```shell
$ git checkout master
$ git rebase spaghetti
First, rewinding head to replay your work on top of it...
Fast-forwarded mater to spaghetti.
```

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase5.jpg)

### An Illusion of Movement

when we are doing a rebase, git cannot actually detach the current branch and move to the top of the target branch and the reason is that commits are database objects and database objects are immutable. If you change anything in a commit (its parent's), then you get a different hash, which means a different commit.

so lets take a step back and imagine what happens if we change the parent of the highlighted commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase6.jpg)

The parent's SHA-1 is stored inside the commit, so the commit data must change and since commits are immutable the commit itself has to change and we are going to get a new hash for the new commit. The same thing is going to happen to the child of this commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase7.jpg)

So, how rebasing really works is that when we rebase, Git makes copies of the commits with the same data except for their parents:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase8.jpg)

 These are new files with new name in the database directory. Finally, Git moves the rebase branch (a_branch in the image) to the new commits and leaving the old commits behind:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase9.jpg)

**Note:** Rebasing is an operation that creates new commits.

### Taking out the Garbage

An object taht cannot be reach through any references, is considered dead and will be removed by a garbage collector. So, after rebasing, if nothing is pointing at the old commits, they are going to get garbage-collected by Git.

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/rebase10.jpg)

### The Trade-offs of Merges

The benefit of the merge is that it preserves the whole history, but the problem is that it sometimes get confusing if we have multiple merges with multiple commits.

### The Trade-offs of Rebases

Rebase history looks very simple and neat and there is no reason for commands such as git log to arbitrarily squash commits into a single timeline because commits are arranged in a single timeline already (rebase refactors history). But the truth is that this niceness is a lie because as you know rebase creates copies of commits and leaves them behind. Also, there there are some advanced Git commands that become less useful if you tamper with project history.

Later we will have an example that shows how mindless rebasing can take you into trouble.

**Note:** When is doubt, just merge. You should only use it, if you know what you are doing and you understand the consequences.

### Tags in Brief

* Git has the ability to tag specific points in history as being important. Typically people use this functionality to mark release points (v1.0, and so on). 

* Tags are pointers to commits.

* There are two types of tags: Annotated and Lightweight:

```bash
git tag `tagName`                       # Lightweight Tag
git tag -a `tagName` -m `tagMessage`    # Annotated Tag
```

* The lightweight tag is just a simple label that contains the SHA-1 of the commit. The annotated tag is similar, but it contains the SHA-1 of a tag object in the database, and that object in turn is referencing a commit. Annotate tag has extra information like: the tagger information, the date the commit was tagged, and the annotation message.

* `git tag` shows the list of the tags.

* the difference between a tag and a branch is that a branch moves but a tag doesn't