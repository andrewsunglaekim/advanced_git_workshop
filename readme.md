# Advanced Git Workshop

## Requisites

This workshop is primarily designed for those that have a decent foundation in git. You don't need to be an expert with git. You should be familiar with and have used the following git commands(in no particular order):

```
init
add
commit
checkout
merge
pull
push
branch
log
status
diff
```

## Learning Objectives

- Describe the three trees of git
- Identify what the HEAD in git is.
- Identify the unidirectional relationship of commits
- Use `git checkout` to inspect commits or change the file tree.
- Define detached HEAD state
- Use bisection (`git bisect`) to identify potentially bad commits.
- Identify how realtive refs (`~` and `^` characters) are used to point at commits.
- Reset git history using reset
- Access reference logs to undo the terrible things.
- Use `git revert` to "revert" a commit
- git merge revsisted
- Use git rebase to rewrite history onto a new branches tip.
- Identify some advantages and disadvantages of workflows involving rebase versus merge.
- Use `git cherry-pick` to choose a commit to apply to the HEAD.
- identify needs to force push to remote repositories

## Framing

Today we'll be diving deeper into some of the lesser used git commands. With a better understanding of some the more advanced git concepts, we can start to leverage really powerful tools at git's disposal.

We'll try to dispel some of the fear caused by git commands by understanding exactly what happens during those commands.

It'll be impossible to cover the entirety of the git ecosystem, but we'll cover some useful tools for advanced git users.

## The three trees

When we think about git, we can boil down a good amount of it's commands with how they influence the three "trees" of git:

#### "The Working Directory"
This tree is in sync with the local filesystem and is representative of the immediate changes made to content in files and directories.

#### The 'Staging Index'.
This tree is tracking Working Directory changes, that have been promoted with git add, to be stored in the next commit.

#### The Commit History
The final tree is the Commit History of HEAD.


## HEAD
It can be thought of as a symbolic reference to the currently checked-out commit. Where we currently are. When you hear something like the tip of the HEAD, it is basically the current commit.

## Rehash commits and branches
Commits add the latest changes to a repo. More specifically they point to the exact moment when the change occurs, what changes occurred, and who made the changes.

Not only that, commits are aware of where they come from. IE. which commit(s) is/are my parent(s).

> Note the `(s)` as commits can have multiple parents from a merge.

So when we say branches point to a commit, they quite literally hold a reference to a commit.

Git leverages this [linked list](https://en.wikipedia.org/wiki/Linked_list) data structure to define git logs and have histories of commits even when commits themselves are only aware of their direct parents.

## `show` and `checkout`
As git users, we can inspect our changes in a variety of ways.

`git show` ... shows us commit information and the git diff from the commit specified as the argument against it's direct parent(s)

```bash
# the argument to show would be some commit sha
$ git show 148d58e
```

To have HEAD point to commit you would instead use `checkout`:

```bash
# the argument to checkout would be some commit sha
$ git checkout 148d58e
```

> Depending on the arguments and flags passed to checkout, checkout can also be used against files in your working directory as well.

The above command would point `HEAD` to that commit. Meaning your working directory would reflect the folders and files of the commit passed in.

This above command will also put you in a detached HEAD state. Meaning it is detached from any reference or branch. It still very much exists and is pointing to the commit you specified.

## Detached HEAD
You might have been in a detached HEAD state before, some git commands put the user into a detached HEAD. If you are to make any commits in a detached HEAD you lose them unless you make a reference to it. IE. a branch.

You can leave a detached HEAD state simply by checking into a branch, something like this:

```
$ git checkout master
```

## Bisection

The first git command we'll dive into today is `git bisect`.

Through git logs we can see that commit histories are linear and "sorted" in terms of sequence of changes.

Git leverages [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm) and user input to help identify bad commits through `git bisect`.

In order to "bisect" your code. You start with a simple command:

```bash
$ git bisect start
```

This will enter your git environment into "BISECTING" mode. If at any point you want to leave, run `$ git bisect reset`.

The next thing that bisect expects is 2 commit sha's. One sha where we know our code base is "correct" and another sha where we know our code base has a bug. The arguments to the following commands are git sha's of a commit history.

```bash
$ git bisect good bc08081
$ git bisect bad b6a0692
```

Now that git bisect knows a known good starting point and an end point

This is why small purposeful semantic commits can be really helpful for code maintainability.





git reset - https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified
- `--hard` is dangerous, because  

git diff - https://stackoverflow.com/questions/1191282/how-to-see-the-changes-between-two-commits-without-commits-in-between

## git merge

You’ll notice the phrase “fast-forward” in that merge. Because the commit C4 pointed to by the branch hotfix you merged in was directly ahead of the commit C2 you’re on, Git simply moves the pointer forward. To phrase that another way, when you try to merge one commit with a commit that can be reached by following the first commit’s history, Git simplifies things by moving the pointer forward because there is no divergent work to merge together — this is called a “fast-forward.”

## REFLOG

You can think of it as a chronological history of everything you’ve done in your local repo.

## Rebasing
(this workshop exercise/lesson repo example 0.0)

## Force Push
This is a scary one for sure.

First off, if we are about to force push to a remote branch and someone else is also using that remote branch. We **DO NOT** do it.

There are some situations where we will want to force push. More often than not, it's because you're rebasing your remote branch locally onto some pure branch like `master` or `test` and you want to push that rebase to the remote.

In a force push, we are quite literally telling the remote branch to stop pointing at its current commit and instead point to the same commit that your local HEAD is pointing to.

This can have pretty disastrous effect if someone else is working off of that branch as they potentially couldn't reconcile changes against it anymore.

In short, be very cognizant when leveraging a force push. And if you're unsure, it's better to ask someone.
