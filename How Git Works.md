# How Git Works

## Git Is Not What You Think

* Git is a map of keys and values
* Value are objects and keys are SHA-1 hash

```git
git hash-object filename                         // generates a hash key for a file
echo "Apple Pie" | git hash-object --stdin       // generates a hash key for a text
git init                                         // create a git repository
```

* The repository is stored in the .git directory

```git
echo "Apple Pie" | git hash-object -w --stdin    // creates the directory and
                                                 // the blob file for the text
// output: bb3918d5053fea31fc9a58fae1e5bdeabe3ec647
```

* Blob files for objects added to the repository are stored in a directory inside object directory. the directory's name starts with the first two characters of the returned hash key (e.g. in the above example)
* The name of the blob file is the rest of the hash key (i.e. remove the first two characters)

* git status displays the state of the working directory and the staging area

```git
// git status output

On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   menu.txt
        new file:   recipes/README.txt
        new file:   recipes/apple_pie.txt
```

* To commit a new file we need to first add them to the staging area
* The staging area is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the “index”, but the phrase “staging area” works just as well.

```git
git add menu.txt                 // add changes to the staging area
                                 // the chagne here is that we have a new file
```

* `commit` records changes to the repository. It stores the current contents of the index in a new commit along with a log message from the user describing the changes.

```git
git commit -m "My Message!"
```

```git
// output for the first commit

[master (root-commit) c3c8b07] First Commit!
 Committer: Doe <john.doe@example.com>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 3 files changed, 2 insertions(+)
 create mode 100644 menu.txt
 create mode 100644 recipes/README.txt
 create mode 100644 recipes/apple_pie.txt
```

* use `git log` to look at the list of the existing commits

```git
commit 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c (HEAD -> master)
Author: Doe <john.doe@example.com>
Date:   Fri Mar 23 12:06:55 2018 -0500

    First Commit!
```

* Use `cat-file` to check the content of an object (e.g. commit, tree, blob, etc.) in the object folder (-t: type. -p: print)

```git
git cat-file -p 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c
```

```git
// output

tree 127ea67bfaa06ad1407ff08bcbadea12e531568c
author Doe <john.doe@example.com> 1521824815 -0500
committer Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521824815 -0500

First Commit!
```

* tree in the above example is the root directory

```git
git cat-file -p 127ea67bfaa06ad1407ff08bcbadea12e531568c
```

```git
100644 blob 9eed377bbdeb4aa5d14f8df9cd50fed042f41023    menu.txt
040000 tree 184c2d41e294290bcaa65d0d0960e8929a210f36    recipes
```

* the numbers at the beginning of the line in the above example are access permissions
* a blob is not really a file it is the content of a file. The file name and the file permissions are not stored in the blob. They are stored in the tree that points to the blob (you will see later why this is a good thing)
* the following image show the object database. The content of the `menu.txt` and `apple_pie.txt` are the same so root and recipes trees both point to the same blob:

![The Object Database](https://github.com/farbod2015/javascript/blob/master/img/objectdb1.jpg)

* if we edit the `menu.txt` and add a new line "Cheesecake" and commit. The content of the new commit will look like this:

```git
git commit -m "Add Cake"
```

```git
tree f6339016a5a44313c05b9e2831c4edb324a69548
parent 9506b33e69f3ba80e43fd79c50043b11d1c2cd4c
author Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521834006 -0500
committer Hesaaraki <Farbod.Hesaaraki@thermon.com> 1521834006 -0500

Add Cake
```

* Commits are linked. In the above example the parent is the first commit.
* The tree of the second commit is different than the tree of the first commit.
* if we check the content of the tree of the second commit it will look like this:

```git
100644 blob b63cba247afc716104ab997704f0973823894907    menu.txt
040000 tree 184c2d41e294290bcaa65d0d0960e8929a210f36    recipes
```

* the blob for the `menu.txt` in the second commit is different than the blob in the first commit since its content has changed but the content of the `recipes` tree has not change so the hash key remains the same
* Here is the file structure of the object database after the second commit:

![The Object Database](https://github.com/farbod2015/javascript/blob/master/img/objectdb2.jpg)

* As you can see we have currently 8 objects in the database. We can check the number of objects using `count-objects`:

```git
git count-objects
```

```git
//output

8 objects, 32 kilobytes
```

* for huge files git maybe store the difference between the two version and it may compress multiple objects into a single blob.
* There is another type of objects in git called tags. A tag is like a label for the current state of the project (i.e. a simple label for an object). There are two types of tags in git _regular tags_ and _annotated tags_.
* Annotated tags are the ones that come with a message. To create an annotated tag, you could use the git tag command with the -a argument, and you need a name for the tag and a message:

```git
git tag -a mytag -m "I love cheesecake"
```

* you can get a list of the tags using `git tag` command
* Annotated tags are also objects like commit. You cat-file tags using their hash key or tag name:

```git
git cat-file -p mytag
```

```git
// output

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

* you can check the list of the branches with `git branch`
* Git normally puts branches in `refs/heads` directory.
* Right now the `heads` folder only contains `master`. It is not a compressed file (unlike blobs) and its content is only a hash key for the current commit.

```git
cat .git/refs/heads/master
```

```git
// output
f4d96566d0a09558fd04a52fb07e407c9d6e97bd
```

* so we have two linked commits in this project, and we also have a master branch which is just a simple reference (pointer) to a commit (in this case last commit) and that is why the directory is called `refs`:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branch1.jpg)

* now lets create a new branch called lisa that contains alternative recipes:

```git
git branch lisa
```

* the new branch is pointing to same commit that master branch is pointing to:

```git
cat .git/refs/heads/lisa
```

```git
//output
f4d96566d0a09558fd04a52fb07e407c9d6e97bd
```

* This is what we have now two commits and two branches, and the branches are pointing at the same commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branch2.jpg)

* the current branch is marked with * on the list of branches:

```git
git branch

// output
  lisa
* master
```

* the current branch is stored in `.git/HEAD`. `HEAD` is a reference to a branch or a pointer to a pointer:

```git
cat .git/HEAD

// output
ref: refs/heads/master
```

* If we change `apple_pie.txt` and commit the new changes then the master branch will point to the new commit and the head is still pointing to the master branch:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/newcommit1.jpg)

* use `checkout` to change the current branch:

```git
git checkout lisa

// output
Switched to branch 'lisa'
```

* two things happen when we do a `checkout`:
    1. `HEAD` now points to `lisa`
    1. our working area changes to the content of the commit pointed at by `lisa`

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/checkout1.jpg)

* so in short `checkout` means move `HEAD` and change the working area

* if we edit the `apple_pie.txt` recipe and commit the chages, Git will add the commit to the object database and move the current branch, lisa, to point at the new commit:

![The Master Branch](https://github.com/farbod2015/javascript/blob/master/img/branching1.jpg)


