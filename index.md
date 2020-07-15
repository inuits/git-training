---
author: Jeroen Budts
title: Git Training
...

## Who am I?
* Jeroen Budts
* Developer / Project Manager
* At [Inuits](http://inuits.eu) - An Open Source Consultancy Company
* [http://budts.be](http://budts.be)
* twitter: @teranex
* e-mail: jeroen@inuits.eu

# Overview

---

* Basic Git usage
* How Git stores it's stuff
* Normal Git usage: _branching, merging, rebasing, .._
* Working with remotes
* Some more advanced things: _subversion, submodules, bisect, stash_

# What is Git?

---

_"Git is a free & open source, distributed version control system designed to
handle everything from small to very large projects with speed and efficiency."_

## Distributed
* The entire repository is on your computer
* No connection to the server required to work, view log
* Very fast

## Open source
* Created in 2005
* Linux Kernel
* Linus Torvalds

# Basic Git Usage

--- 

## Initialize repository
```zsh
git init
```

## Then add files
```zsh
git add -A
# or
git add .
# or
git add myfile.txt
git add *.rb
git add directory/
```

## And commit the files

```
git commit
```

Git will ask you for a commit message.

## Committing modified files.  

Stage the files (more on that later):

    git add file.txt

And commit:

    git commit

## Skip staging

Or skip the staging step and immediately commit:

    git commit -a

## Removing a file

    git rm file.txt

And commit

    git commit

## Try it
1. create a new repository
1. add some text-files
1. commit
1. make some changes
1. commit (with `-a` for now)
1. remove a file
1. commit (with `-a`)

# The Index

---

A.K.A. The Staging Area

The index contains the changes that will be **added** to your next **commit**. Your
commit will _not_ contain the changes in your working directory.  

**Only the changes that were added to the index will be in the commit**

---

<img src="images/git-02.png" alt="">

---

If you add a file, or a part of a file, to the index, a copy is made of that
file. When you commit, it is that copy which ends up in the commit.

---

**Important**: if you make changes to the file after adding it to the index, those changes will 
not end up in your commit, unless you add the file again to the index!

---

If you modify files, you need to '_stage_' them again, by running

    `git add`

---

You can also add only parts of a modified file:

    git add -p myfile.txt

**Tip**: use a tool for this

# Checking the state of your repository

---

Checking the state of the working copy

    git status

![status](images/status.png)

## Seeing what has changed

```zsh
    git diff  
    # shows the diff of your working copy

    git diff --cached
    # show the diff of your index
```

**Tip**: use a tool for this

## The log

This shows the log of your current branch

    git log

![log](images/git-13.png)

---

`git log` has many options. For example:

    git log --graph --pretty=format:'%Cred%h%Creset 
        -%C(yellow)%d%Creset %s %Cgreen(%cr)
        %C(bold blue)<%an>%Creset' --abbrev-commit
        --date=relative

results in:
![git-11](images/git-11.png)

---

**Tip**: add this in your global gitconfig as an alias:

```ini
    [alias]
        l = log --graph --pretty=format:'%Cred%h%Creset
            -%C(yellow)%d%Creset %s %Cgreen(%cr)
            %C(bold blue)<%an>%Creset' --abbrev-commit
            --date=relative    
```

---

See the log of all the branches:

    git log --all

![log --all](images/log-all.png)

## Try it
1. edit two text files
1. check the status
1. add one of them to the index
1. commit
1. add the other to the index
1. edit the file again
1. check the status
1. check the diff and diff --cached

# Undoing things
## Undoing changes to a file

```zsh
    git checkout -- file.txt
    # the -- are optional, to tell git it's about a file
    git checkout directory/
```

But **not**

    git checkout branch-name

**Note**:
This gets back the version from the **index**.
If the file is not in the index, it will get the file from the repository.

## Resetting from the index
To reset the file after it was added to the index:

    git reset file.txt

The working copy is now the version from the index and the file is not in the index anymore.
Then you can use

    git checkout file.txt

To completely reset it to the version in the repository.

## Removing unversioned files

```zsh
git clean -f
# to remove untracked files

git clean -f -d
# to remove untracked directories
```

## Completely resetting your working copy

```zsh
git reset --hard

# optionally followed by
git clean -f
```

**Use with care!**

## Reverting a commit
After a commit was already pushed out to other people.

    git revert <commit-hash>

This will create a commit to undo everything introduced by &lt;commit-hash&gt;

## Try it
1. edit a file
1. undo the edit
1. edit the file and add it to the index
1. edit the same file, get the version back from the index
1. create a new file
1. use `git clean` to remove it

## Try it
1. edit a file and commit
1. revert the commit with `git revert`
1. check the git log
1. edit some more files and revert everything with `git reset --hard`

# How Git stores data

---

Knowing this gives you a better understanding of Git

A Git repository is a  
**collection of objects**

## Types of objects
* blobs
* trees
* commits
* tags

## blobs
Git stores the **contents of a file** in a 'blob'.

* does not contain any meta data
* a blob never changes
* a hash is calculated as the blob name
* the hash will always be the same for the same contents

---

some examples:

    $ git hash-object hello.txt
    ce6c1fd146f65c899e6b10e46c89097c644e3229
    
    $ git hash-object say-hi.rb
    a8784b043f12b4b0c9114c55ebf33f5c9b44ce8f

## Trees
A tree is like a **directory**

* Can contain references to 1 or more blobs
* Can contain references to 0, 1 or more other trees
* Contains the meta data about the files
* Itself also identified by a hash

---

![git-03](images/git-03_2.png)

## Commits
If you think about Git, think about commits!

* Contains a reference to one tree object
* Contains references to one or more other commits
    * one exception: the very first commit in the repo
* A commit usually points to it's parent commit
* In case of a merge, it can point to two or more commits (one commit for each branch which you merged together)

---

![git-04](images/git-04.png)

---

If you understand commits,  
you basically understand Git.

## references ('refs')
Because a commit hash is very difficult to remember and not really useful to work with, Git uses references to point to specific commits.

## HEAD
One such reference is **HEAD**

It points to the commit which is currently checked out into your working directory

## master
Another important reference is **master**

* master is the 'default' branch in Git
* when working on the master branch, the master reference and the HEAD reference point to the same commit

## special

* **`HEAD^` & `HEAD^^`**: The commit before HEAD, two commits before HEAD
* **`master~7`**: 7 commits before master reference

# Branches

---

references to other commits

* very easy to create
* a branch has a name
* this name is also the reference to the most recent commit for that branch
* a commit can be shared by branches

## Creating a branch
First create the branch

    git branch mywork

Then start working on that branch:

    git checkout mywork

Or combine the two steps

    git checkout -b mywork

## deleting a branch
Deleting a local branch is also possible

    git branch -d mywork

## branching
* We are working on a branch named 'origin'
* At commit C2 we decide to split off a new branch named 'mywork'.
* Both branches originate from commit C2; commit C3 and C5 have the same parent

---

![git-05](images/git-05.png)

## merging
When you have been working on a (feature) branch for a while you will probably want to merge those branches back together.

```zsh
    # to merge the origin branch back into your mywork
    # branch (to bring it up to date)
    # checkout the target branch
    git checkout mywork
    # merge the branch into the current branch
    git merge origin
```

---

Now your repository looks like this: (Notice that commit C7 has two parents)

![git-06](images/git-06.png)

## Merging

When merging two or more branches there are two possibilities:

* Merge commit
* Fast Forward  

## Merge commit
* When both branches have new commits a merge commit is created.
* Git automatically proposes a commit message:  

    `Merge branch 'mywork' into master`

* The commit has two or more parents

## Fast Forward
* When the target branch does not have new commits
* No merge commit is created
* In fact nothing much happens
* Except: The reference for the target branch is simply changed to point the same commit as the source branch
* This is an ideal situation and can never go wrong
* Obviously you are not always this lucky (→ rebasing)

---

![git-07-before](images/git-07-before.png)  
Merge the 'mywork' branch into origin

---

![git-07-after](images/git-07-after.png)  
The origin branch is simply fast-forwarded

## Explicitly create merge commit
Sometimes you want a merge commit, even when a simple Fast Forward is possible.

    git merge --no-ff

## Try it
1. Create a new branch and check it out
1. Edit a file and commit
1. Checkout the master branch
1. Merge the file
1. Edit files on both branches
1. Merge both branches

## Try it
1. Edit files on both branches, introducing conflicts
1. Merge both branches
1. Resolve the conflicts
1. Commit

# History

## Git log

## Inspecting an earlier commit

    git show 2a2ea8d
    git show HEAD^^

Gives you a **diff** of what exactly that commit changed **+ metadata** for that commit

## Checkout
You can checkout a commit to see the repository as it was back then

    git checkout <commit-hash>

**Note**: Git will tell you that you are in a _detached head_ state, because your HEAD reference
doesn't point to any branch.

## Resetting to an earlier commit

You can reset your branch to an earlier commit, to throw away the newer commit(s).

    git reset HEAD^^

This will change the reference for the branch to that commit

**Note**: only do this if you have not yet pushed these newer commits

## Reflog
The reflog is a log of everything which happened in your repository

    git reflog

Useful when you make a mistake during rebasing, merging or resetting.

Your old commits are not 'lost'. They are simply not used anymore and will be removed by `git gc` after a while.

## Try it
1. edit a file
1. commit
1. reset to the previous commit
1. use the reflog to find the newer commit
1. reset back to the newer commit (no more changes)
1. reset back to older
1. make additional changes to the file
1. commit the file
1. check the reflog

# Rebasing

---

Instead of merging (with merge commits) you can also rebase (so you can then fast forward)

---

Some people will tell you that this is very harmful, it can break your repository and destroy the universe.  
This is **NOT TRUE**. (At least if you know what you are doing)

## What is 'rebasing'?

By rebasing your commits you can actually rewrite your history:

* Edit a commit message
* Add missing files to a commit
* Reorder your commits
* Modify the parent of a commit
* Merge a few commits together into a single commit
* Delete commits from the history
* ...
* And break your repository if you want :)

## How not to destroy your repository?

---

<span style="font-size: 150%; ">Do <strong>not</strong> rebase commits which have already been pushed to other people</span>

---

<span style="font-size: 150%; ">Do <strong>not</strong> rebase commits which have already been pushed to other people</span>

---

<span style="font-size: 150%; ">Do <strong>not</strong> rebase commits which have already been pushed to other people</span>

---

Each commit which is rebased will get a new, different, hash. People (and Git) which pull this new hash will get confused.

---

If you do rebase a commit which was already pushed, Git will refuse the new commit, unless you use the `--force` option.

## Amending changes

* The easiest and 'safest' kind of rebase 
* Only possible for the most recent commit
* Let's you add missing files and modify the commit message  

After modifying your index again:

    git commit --amend

## Rebasing on top of another branch

This is an alternative approach to merging, with a merge commit.

---

Let's reuse the example:

![git-05](images/git-05.png)

---

Now you  want to merge 'mywork' into 'origin' without creating a merge commit

---

What we did before:

```zsh
    git checkout origin
    git merge mywork
```

---

What we will do now:

```zsh
    # on the mywork branch
    git rebase origin
    # fix any merge conflicts
    git checkout origin
    git merge mywork
```

---

```zsh
    # on the mywork branch
    git rebase origin
```

![git-08](images/git-08.png)

---

![git-09](images/git-09.png)

---

![git-10](images/git-10.png)

## Interactive rebasing
With interactive rebasing you can really rewrite history the way you want it to be. ...And break your repository.

Rebase the commits since the specified commit-hash

```zsh
    git rebase --interactive <commit-hash>
```

---

Suppose we have the following commits

![git-11](images/git-11.png)

---

To rebase the most recent 3 commits:

```zsh
    git rebase --interactive 4efd195
```

![git-12](images/git-12.png)

## Try it
1. edit a file and commit
1. edit the file again
1. commit the file with the `--amend` option

## Try it
1. create and checkout a branch 
2. edit a file and commit
3. checkout the master branch
4. edit the same file and commit
5. do a merge
6. check the log

---

7. undo the merge (reset &amp; checkout)
8. checkout the branch
9. rebase the branch on top of master
10. checkout master and merge
11. check the log
12. Bonus: delete the merged branch

# Sharing code

## Remotes
To share work with other people, you can add one or more remotes

* remotes have an address
* remotes have a name
* you can add multiple remotes to a repository

---

Easiest method to set this up is by cloning an existing repository instead of initializing your repo.

```zsh
    git clone http://git.drupal.org/project/drupal.git
```

![git-14](images/git-14.png)

This will set up everything for you

## Manually adding remotes

Sometimes you will want to add a remote

* Because you had already created the repository
* Or maybe because you want to add one or more additional remotes

## Adding a remote
This is done with the `git remote` command

    git remote add github git@github.com:teranex/git-talk.git

This will add my Github repository for this presentation as a remote with the name _github_

## Tip: pretty url's for remotes
You can add shortcuts in your git config for often used url's

    git config --global url.git@github.com.insteadOf gh
    git config --global
        url.ssh://myuser@mydomain.net/repos/.insteadOf r

Then you can use short url's to clone

    git clone gh:teranex/dotvim
    git clone r:myrepo.git

## Pulling and pushing

When you have cloned the repository:

    git pull

This will pull in the changes from the current branch on the origin

    git push

Will push your changes to the origin

**However**: This will only work for '_tracking_' branches.

## Remote Branches

It is important to think about remote branches as just _branches_

By default, Git does not know, nor care, about relationships between branches!

---

* local branch: **master**
* remote ('github') branch: **github/master**
* **Git just sees two branches**  

    `git pull github master`  
    `git push github master`

* By default, no local branches are created for remote branches

## Remote Branches

You can get a good overview of all your local and remote branches and how they are tracking with:

```zsh
    git branch -avv
```

![img-19](images/git-19.png)

## Tracking branches

To create a local branch based on a remote branch:

    git checkout --track -b mywork github/mywork

To link an existing local branch to a remote branch:

    git branch --setup-stream github/mywork

---

You can verify this in the git config file (.git/config) in your repository:

![git-15](images/git-15.png)

## Merging while pulling

To better understand pulling, let's see what actually happens. Instead of using `git pull`, you also do (while on the master branch):

```zsh
    # pull in the new objects
    git fetch github

    # merge the remote branch with the local branch
    git merge github/master
```

This works exactly the same as merging two local branches!  

## Avoiding useless merge commits

Merging a remote branch in your local branch can create a useless merge commit:

![git-16](images/git-16.png)

---

You can avoid this by rebasing instead of merging:

```zsh
    git pull --rebase
```

or, if you want to do it manually:

```zsh
    git fetch github
    git rebase github/master
```

%% ## Try it
%% 1. Clone a repository
%% 1. Checkout a remote branch (you are now in a detached head state)
%% 1. Create a local branch for the remote branch

# Stash

---

Sometimes you want to set aside your changes

* because you want to rebase (which is not possible in a dirty repository)
* to try alternative approaches
* ...

---

to stash everything

    git stash

Or you can give a description

    git stash save "something fancy I was working on"

---

to see the stashes

    git stash list

to get changes back from the stash

    git stash pop

---

I often use the stash when I want to `git pull --rebase`, while I have uncommitted changes (git will refuse to do it in that case):

    git stash
    git pull --rebase
    git stash pop

## Try it
1. make some edits
1. stash them
1. unstash them


# Submodules

---

In a Git repository you can 'link' other repositories to subdirectories. This can be useful, for example when using external libraries or when building your VIM configuration, to pull in all the plugins.

---

Similar to svn:externals, but not quite the same:

* submodules must be registered in the repository
* submodules must be pulled-in separately for each repo.
* submodules are not updated automatically
* submodules are not pushed automatically
* a specific commit is referenced in the parent repo

## Add a submodule

    # from the root of the repository
    git submodule add git://path/to/repo path/in/repo

This will modify the .gitmodules file, which is part of the parent repository, and record the exact commit which is checked out in the submodule.
Now you can commit, push, etc.

## Pulling in submodules

When another repository pulls in the change the following steps are required:

    git submodule init

This will update the .git/config file for that repository and register the submodule in the repository

    git submodule update

This will check-out all the submodules to the correct commit

    # or combine both steps
    git submodule update --init

## Updating submodules
To update you can checkout another commit in the submodule (for example by pulling) and commit the reference to this new commit in the parent repository.

To update all the modules, you can use something like:

    git submodule foreach git pull origin master

## Try it
1. Create a new repo with a commit
1. Add it as a submodule of your main repo
1. Make some changes in the Submodule
1. Check the status of the main repo
1. Commit in the Submodule
1. Check the status of the main repo
1. Commit the updated main repo to point to the ne commit in the  submodule

# Bisect

---

Helping you to find which commit introduced the bug

---

Sometimes somebody will introduce bugs into your software or brake previously working features. Who knows, maybe even you! While trying to find the source of the problem, it can be useful to know which commit exactly introduced the troubles in paradise. That is exactly what Git bisect is for.

## Steps for bisecting
1. Inform Git about one 'good' commit
1. And about one 'bad' commit
1. Git will checkout a commit for you
1. Verify that commit
1. Tell Git whether that commit has the problem ('bad'), or not ('good')
1. Finally the 'bad' commit will be found

## an example
Let's say you know the feature was already broken in the previous commit and 10 commits ago the feature still worked.

---

Start Git Bisect and inform Git about this:

    git bisect start
    git bisect bad HEAD^
    git bisect good HEAD~10

---

Git will checkout the commit in the middle, so you can test:

    Bisecting: 5 revisions left to test after this
    (roughly 3 steps)

---

After testing you inform Git about the result

    git bisect good # when the feature worked
    git bisect bad  # when the feature is b0rken

---

After you have found the bad commit, reset your repository:

    git bisect reset

If you write a script which can verify each commit, you can let Git run it for every commit!

## Try it
1. Find the commit which changed `A tree is a like a **directory**` into `A tree is like a **directory**`

# Git and Subversion

Git has plugins available to migrate from and/or integrate into other versioning systems as well. One such plugin is **git-svn**.

## git-svn
With git-svn you can:

* migrate an existing subversion repository to a git repository, including all the history.
* use Git locally to do your work, but push to a central Subversion server.

## Local Git, Subversion server
To locally use Git and push to a central Subversion server:

First 'clone' the Subversion repository into a local Git repo

```zsh
git svn clone -s http://svn.example.com/myproject
# the -s means the subversion repo has a
# standard layout (trunk/ etc)
```

---

Now you can work as usual with your Git repository. Except... instead of running ``git pull`` to get the changes from other people, you now do:

    git svn rebase

And you don't do ``git push``, but:

    git svn dcommit

# Suggested reading

---

* the git man-pages
* the .git-folder of a repository (just take a look, it's interesting)
* Pro Git: [http://progit.org/](http://progit.org/)
* The Git Community Book: [http://book.git-scm.com/](http://book.git-scm.com/)
* StackOverflow: [http://stackoverflow.com](http://stackoverflow.com/questions/tagged/git) has a lot of valuable questions and answers related to Git.

# Question?

---

Thank you for your time
