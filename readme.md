# Advanced Git Workshop

## Requisites

This workshop is primarily designed for those that have a decent foundation in git. You should be familiar with and have used the following commands(in no particular order):

```
git commit
git add
git checkout
git 
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

We'll try to dispel some of the fear caused by git.

It'll be impossible to cover the entirety of the git ecosystem, but we'll cover some useful tools for advanced git users.

- semantic commit messages
- coupled with small purposeful commits
- not using `git add .`
- 

### HEAD
it can be thought of as a symbolic reference to the checked-out commit.

**linked list*

### bisection

This is why small purposeful semantic commits can be really helpful for code maintainability. 

## The three trees
"The Working Directory". This tree is in sync with the local filesystem and is representative of the immediate changes made to content in files and directories.

The 'Staging Index' tree. This tree is tracking Working Directory changes, that have been promoted with git add, to be stored in the next commit.

The final tree is the Commit History of HEAD.



When we think about git, we can boil down a good amount of it's commands with how they influence the three "trees" of git. 
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