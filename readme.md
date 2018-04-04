# Advanced Git Workshop

## Requisites

This workshop is primarily designed for those that have a decent foundation in git. You don't need to be an expert with git. You should be familiar with and have used the following git commands(in no particular order):

```
init
clone
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

Now that git bisect knows a known good starting point and an end point, it will start stepping us through commits between the good and bad commits in a binary search fashion.

It'll start at the midway point. It will checkout to the commit that lies as close to the middle between the good and bad commits. It then expect a response between:

- `$ git bisect good`
- `$ git bisect bad`

If we inspected our code and it's bug free, we would input `$ git bisect good`.

If instead our code had a bug, we would input `$ git bisect bad`.

Depending on the choice we make, git will move the HEAD to the next mid point commit.

If we choose `bad`, it will move HEAD to the midpoint commit in the first half

If we choose `good`, it will move HEAD to the midpoint commit in the second half.

Git repeats this until it identifies the first commit where the code went wrong. Unless we never tell it `bad`.

This is yet another reason why small purposeful semantic commits can be really helpful for code maintainability.

## Git Bisect - You do

Clone down [this repo]() . Open the `index.html` in the browser.

Oh no, all we can see is red. We knew at one time this feature had some pretty pertinent text, but now all we can see is red.

You take a look at the commit history of this repo and you are appalled. There's over a hundred commit and they all have the same commit message.

#### Objective:
Pinpoint the commit in which the red background bug first occurs.

#### Directions:
1. Do not look at the code.
2. Only use browser and terminal.(*hint* refresh your browser after each stage of the bisect)
3. Use `$ git log --oneline` to find the first and last commits
4. Use those commits as the `good` and `bad` commits for your `git bisect`
5. Find the first `bad` commit.

## Targeting commits

Many features including `git bisect` leverage commit histories to target specific commits. In the case of bisection, git uses midway points of commit histories. As users of git, we're able to target commits in many ways.

One way we saw earlier was providing an exact commit sha. You can also use branches as they point to a commit. What if you wanted something relative to a branch or the HEAD?

Enter `~` and `^`. You've probably seen these before in various stack overflow posts or git blogs. Turns out they have actual meaning!

In the simplest terms:

- `~` means how many ancestors back
- `^` means which ancestor in the event a commit has multiple parents(think merges)

These operators can also be chained. Here are some examples of `~`:

```
HEAD~1 # The previous commit of the current head
HEAD~2 # The commit that is 2 before the current head
master~1 # The previous commit of the tip of the master branch
ce70821~1 # The previous commit of the commit sha ce70821
```

Merging results in a merge commit, therefore that commit will have 2 parents. One parent is the branch we are currently on. The other parent is the branch we move into. For the purposes of the `^`. The branch we are on when merging is the "first" parent and the branch we want to merge in is the "second" parent. If HEAD points to a commit from that resulting merge:

```
HEAD^1 # this would point to the HEAD commit's first parent. The branch we were on before the merge.
HEAD^2 # this would point to the HEAD commit's second parent. The branch we merged in.
```

You can also chain these operators, something like this:

```
HEAD~3^2
```

> This would point to the commit that would be the second parent of the commit that is 3 commits behind the `HEAD`

## git reset
The following command could have disastrous effects on your `HEAD`'s history. Be very careful when resetting.

`git reset` is a versatile tool for undoing changes. It is easiest to think about reset and its different flags in how it changes and modifies the three tree of git.

> In the following snippets instead of sha's, we will use letters to represent our commit history. `A` being the first commit in the history and `D` being the last commit in the history. HEAD is currently on commit `D`.

#### resetting the `--soft` way.
In all resets, the commit history(3rd tree) is always changed to reflect the commit passed in.

```
$ git reset --soft B
```

This will set `HEAD` and current branch reference to `B`. Whether you pass in `--soft`, `--mixed`, or `--hard`, it will always change the commit history in this way.

When using the `--soft` flag. The diff between `B` and `D` will be staged for commit to reflect a commit that would encompass the changes from `B` to `D`. In other words the "second tree" (the index) has those differences. The `--soft` really just "resets" the commit history(3rd tree).

#### resetting the `--mixed` way(this is the default)
This is the default mode of reset.

```bash
$ git reset --mixed B
# or
$ git reset B
```
Just like in `--soft` the `HEAD` and current branch ref will now point to B. However, the diff between `B` and `D` is not staged. However, that diff still exists in the working directory. So mixed resets both the staging area and the commit history(2nd and 3rd trees) but the working directory(1st tree) will have the changes from `C` and `D`

#### resetting the `--hard` way
This is by far the most dangerous type of reset. We would only want to use a hard reset if we wanted to time travel to exactly the state of a commit.

```bash
$ git reset --hard B
```

All three tree's in this case would "reset" to the state of commit `B`. This means we would lose all traces of commit `C` and `D` and essentially not be able to access them ever again! or not...?

## Git Reset - You do

Now that we've identified the bug in our application. Let's do a hard reset on our application to the commit previous to the bad commit specified in the `bisect` portion.

Hint use `~` against the bad commit sha to find that commit.

## git reflog
With all these dangerous things we can do in git, surely there must be some way for us to backtrack on disastrous commands. There is! Enter the reference log.

The default expiration time for reflog entries is 90 days. So it's good to fix any immediate mistakes with the reference log, but certainly isn't going to be a possible solution if we've waited too long.

#### references

References, the namesake of `reflog` more commonly known as branches, point to a commit. Quite literally, there is a file with the branch's name in the `.git` directory that has a commit sha of the the commit it is pointing to. Go into any repository with a master branch and run this command:

```bash
$ cat .git/refs/heads/master
```

Then compare that with the tip of `master`'s git log. All this to say, branches point to a commit.


#### `reflog`
Git tracks these branches in a place that isn't ... the branches. It's the `reflog`. Reference logs record when the tips of branches and other references were updated in the local repository. Reflogs are useful in various Git commands, to specify the old value of a reference.
The most generic `reflog` is simply:

```bash
$ git reflog
```

What you'll see is the default reference log for the `HEAD`. Any change `HEAD` makes, `reflog` will list it out. Each line contains:

- abbreviated commit sha
- a pointer or reference
- action type(eg. commit, reset, checkout, etc.)
- commit message or description

Often times parsing the default `reflog` can be pretty terse and hard to reason about. There are lot's of commands, arguments and flags that can be passed to the `reflog` command.

This one for example will instead of showing each step `HEAD` goes through, will show a relative time/date instead.

```bash
$ git reflog --relative-date
```

One useful one is:

```bash
$ git reflog show --all
```

We can see all actions that are updates to the tips of all the branches. It will filter a bit of the excess noise that we get from the default reference log.

We can additionally specify branches to show the reference log for a specific branch:

```bash
$ git reflog master
```

We could combine some commands, something like this:

```bash
$ git reflog --relative-date master
```

We can even pass in times:

```bash
$ git reflog master@{1.day.1.hourago}
```

The above command will output a reference log for master that starts with any changes made 25 hours prior to current time.

We can pass in steps as well like `HEAD@{2}`. And this would be the reference log for all changes prior to 2 changes from the current state of `HEAD`.

#### Leveraging pointers or commit sha's to rewrite history.
The reflog gives us everything we would want in terms of rewriting history to historical references. We can use either the sha or pointer of a `reflog` entry.

Examples:
Our `reflog` for master might look something like this:

```
33e8ab2 master@{0}: commit: 3rd commit
e054213 master@{1}: commit: 2nd commit
d57537e master@{2}: commit: 1st commit
bbc0c23 master@{3}: commit: initial commit
```

We could time travel in these ways:
```
# each pair is functionally equivalent
$ git checkout e054213
$ git checkout master@{1}

$ git reset --soft d57537e
$ git reset --soft master@{1}
```

> these "pointers" can be used in other commands to. With something like `git diff`, we could examine the differences in our branch from the current state to a time interval we specify.




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
