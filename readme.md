# Advanced Git Workshop

## Requisites

This workshop is primarily designed for those that have a decent foundation in git. We don't need to be an expert with git. We should be familiar with and have used the following git commands(in no particular order):

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

## Learning Objectives (3/3)

- [Describe the three trees of git](https://github.com/andrewsunglaekim/advanced_git_workshop#the-three-trees-38)
- [Identify what the HEAD in git is](https://github.com/andrewsunglaekim/advanced_git_workshop#head-210)
- [Identify the unidirectional relationship of commits](https://github.com/andrewsunglaekim/advanced_git_workshop#rehash-commits-and-branches515)
- [Use `git show` and `git checkout` to inspect commits or change the file tree](https://github.com/andrewsunglaekim/advanced_git_workshop#rehash-commits-and-branches315)
- [Define detached HEAD state](https://github.com/andrewsunglaekim/advanced_git_workshop#detached-head321)
- [Use bisection (`git bisect`) to identify potentially bad commits](https://github.com/andrewsunglaekim/advanced_git_workshop#bisection-1536)
- [Identify how realtive refs (`~` and `^` characters) are used to point at commits](https://github.com/andrewsunglaekim/advanced_git_workshop#targeting-commits-with-git-revisions-555)
- [Reset git history using reset](https://github.com/andrewsunglaekim/advanced_git_workshop#git-reset-1570)
- [Access reference logs to undo the terrible things](https://github.com/andrewsunglaekim/advanced_git_workshop#git-reflog1085)
- [Use `git revert` to "revert" a commit](https://github.com/andrewsunglaekim/advanced_git_workshop#git-revert595)
- [Identify how a merge results in a commit](https://github.com/andrewsunglaekim/advanced_git_workshop#git-merge-5105)
- [Use git rebase to rewrite history onto a new branches tip](https://github.com/andrewsunglaekim/advanced_git_workshop#rebasing-5110)
- [Identify some advantages and disadvantages of workflows involving rebase versus merge](https://github.com/andrewsunglaekim/advanced_git_workshop#merge-vs-rebase)
- [Identify needs to force push to remote repositories](https://github.com/andrewsunglaekim/advanced_git_workshop#force-push5120)
- [Identify the need to prioritize git workflows on teams](https://github.com/andrewsunglaekim/advanced_git_workshop#communication-5125)
- [Bonus!: Other useful commands](https://github.com/andrewsunglaekim/advanced_git_workshop#bonus-helpful-commands-that-dont-really-fit-into-the-flow-of-the-lesson-plan)

> Much of this workshop is derived from two sources. [Git Book or Pro Git](https://git-scm.com/book/en/v2) and [Atlassian Tutorials](https://www.atlassian.com/git/tutorials). Highly recommend both for a wealth of knowledge on git. Most of the headers in this workshop's markdown are links to one of those two resources in the specified topic.

> Headers that contain `"- You do"` will link to [a different repo](https://github.com/andrewsunglaekim/advanced_git_workshop_ex) that has an exercise associated with it.

## Framing (2/5)

Today we'll be diving deeper into some of the lesser used git commands. With a better understanding of some the more advanced git concepts, we can start to leverage really powerful tools at git's disposal.

We'll try to dispel some of the fear caused by git commands by understanding exactly what happens during those commands.

It'll be impossible to cover the entirety of the git ecosystem, but we'll cover some useful tools for advanced git users. As well as establish a foundation of knowledge for the taxonomy we use in git.

## [The three trees](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) (3/8)

> Video 1 => 5:10

When we think about git, we can boil down a good amount of it's commands with how they influence the three "trees" of git:

#### "The Working Directory"
This tree is in sync with the local filesystem and is representative of the immediate changes made to content in files and directories.

#### The "Staging Index".
This tree is tracking Working Directory changes, that have been promoted with git add, to be stored in the next commit.

#### The Commit History
The final tree is the Commit History of HEAD. More specifically, it is the final snapshot that HEAD is pointing to and the parent of the next commit.


## HEAD (2/10)

> Video 1 => 6:31
It can be thought of as a symbolic reference to the currently checked-out commit. Where we currently are. When we hear something like the `HEAD` is on the tip of master, then we would be on the master branch.

## [Rehash commits and branches](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)(5/15)

> Video 1 => 6:50

Commits are changes to a repository. More specifically they point to the exact moment when the change occurs, what changes occurred, and who made the changes.

Not only that, commits are aware of where they come from. IE. which commit(s) is/are my parent(s).

> Note the `(s)` as commits can have multiple parents from a merge.

So when we say branches point to a commit, they quite literally hold a reference to a commit.

Git leverages this [linked list](https://en.wikipedia.org/wiki/Linked_list) data structure to define git logs and have histories of commits even when commits themselves are only aware of their direct parents.

## [`show`](https://git-scm.com/docs/git-show) and [`checkout`](https://git-scm.com/docs/git-checkout)(3/18)

> Video 1 => 11:42

As git users, we can inspect our changes in a variety of ways.

`git show` ... shows us commit information and the git diff from the commit specified as the argument against it's direct parent(s)

```bash
# the argument to show would be some commit sha
$ git show 148d58e
```

To have HEAD point to a commit we would instead use `checkout`:

```bash
# the argument to checkout would be some commit sha
$ git checkout 148d58e
```

> Depending on the arguments and flags passed to checkout, checkout can also be used against files in our working directory as well. `git checkout -- fileName` or `git checkout fileName` use the prior incase we had a branch called `fileName`. Checkout, in this case, would "reset" that file to whatever the `HEAD`'s version of that file is

The above command would point `HEAD` to that commit. Meaning our working directory would reflect the folders and files of the commit passed in.

This above command will also put us in a detached HEAD state. Meaning it is detached from any reference or branch. The `HEAD` still very much exists and is pointing to the commit we specified.

## [Detached HEAD](https://git-scm.com/docs/git-checkout#_detached_head)(3/21)
It means simply that HEAD refers to a specific commit, as opposed to referring to a named branch. We might have been in a detached HEAD state before, some git commands put the user into a detached HEAD.

If we are to make any commits in a detached HEAD we lose them unless we make a reference to it. IE. a branch.

We can leave a detached HEAD state simply by checking into a branch, something like this:

```
$ git checkout master

# or if we want to point the commit to a new reference
$ git checkout -b some-branch-name
```

## [Bisection](https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git) (15/36)

> Video 1 => 16:40

There's been lots of times where we implement a feature, we think it's solid and nothing can go wrong with it. Then we continue on the project, 4 or 5 more features later, the initial feature we implemented breaks. We have no idea which feature let alone a commit that broke the code. It would be really difficult to pin point the exact commit that breaks the code. Enter `git bisect`.

Through git logs we can see that commit histories are linear and "sorted" in terms of sequence of changes.

Git leverages [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm) and user input to help identify bad commits through `git bisect`. Let's see how it works.

In order to "bisect" our code. We start with a simple command:

```bash
$ git bisect start
```

This will enter our git environment into "BISECTING" mode. If at any point we want to leave, run `$ git bisect reset`.

The next thing that bisect expects is 2 commit sha's. One sha where we know our code base is "correct" and another sha where we know our code base has a bug. Example:

```bash
$ git bisect good bc08081
$ git bisect bad b6a0692
```

> The arguments to the following commands are git sha's of a commit history. If our bad commit is at the `HEAD`, then we can run `$ git bisect bad` without an argument.

Now that git bisect knows a known good starting point and an end point, it will start stepping us through commits in a binary search fashion.

It'll start at the midway point. It will checkout to the commit that lies as close to the middle between the good and bad commits. It then expects a response between:

- `$ git bisect good`
- `$ git bisect bad`

If we inspected our code and it's bug free, we would input:

```
$ git bisect good
```

If instead our code had a bug, we would input:

```
$ git bisect bad
```

Depending on the choice we make, git will move the HEAD to the next mid point commit.

If we choose `bad`, it will move HEAD to the midpoint commit in the first half

If we choose `good`, it will move HEAD to the midpoint commit in the second half.

Git repeats this until it identifies the first commit where the code went wrong. Unless we never tell it `bad`.

This is yet another reason why small purposeful semantic commits can be really helpful for code maintainability. If we're writing code that needs to compile, this is a great reason to make sure to only commit code that compiles.

## [Git Bisect - You do](https://github.com/andrewsunglaekim/advanced_git_workshop_ex#exercise-1) (14/50)

Clone down [this repo](https://github.com/andrewsunglaekim/advanced_git_workshop_ex) . Then run `$ git remove remote origin`. Open the `index.html` in the browser.

Oh no, all we can see is red. We haven't worked on this code base in forever.  We knew at one time this feature had some pretty pertinent text, but now all we can see is red.

You take a look at the commit history of this repo and you are appalled. There's over a hundred commits and they all have the same commit message.

#### Objective:
Pinpoint the commit in which the red background bug first occurs.

#### Directions/Rules:
1. Do not look at the code.
2. Only use browser and terminal.(*hint* refresh your browser after each stage of the bisect)
3. Use `$ git log --oneline` to find the first and last commits(`ctrl` + `d/u` will allow you to page down/up your `git log`)
4. Use those commits as the `good`(first) and `bad`(last) commits for your `git bisect`
5. Find the first `bad` commit.
6. Record this commit sha for the following exercises in this workshop

## [Targeting commits with git revisions](https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection) (5/55)

> Video 2 => 00:50

Many features including `git bisect` leverage commit histories to target specific commits. In the case of bisection, git uses midway points of commit histories. As users of git, we're able to target commits in many ways.

One way we saw earlier was providing an exact commit sha. We can also use branches as they point to a commit. What if we wanted something relative to a branch or the HEAD? IE. I want the parent of the the current commit `HEAD` is on.

Enter `~` and `^`. We've probably seen these before in various stack overflow posts or git blogs. Turns out they have actual meaning!

In the simplest terms:

- `~` means how many ancestors back
- `^` means which ancestor in the event a commit has multiple parents(think merges)

Here are some examples of `~`:

```
HEAD~ # The previous commit of the current head
HEAD~1 # The previous commit of the current head
HEAD~2 # The commit that is 2 before the current head
master~1 # The previous commit of the tip of the master branch
ce70821~1 # The previous commit of the commit sha ce70821
```

> `~` and `^` by themselves always refer to the first parent.

Merging results in a merge commit, therefore that commit will have 2 parents. One parent is the branch we are currently on. The other parent is the branch we move into. For the purposes of the `^`. The branch we are on when merging is the "first" parent and the branch we want to merge in is the "second" parent. If HEAD points to a commit from that resulting merge:

```
HEAD^ # this would point to the HEAD commit's first parent. The commit we were on before the merge.
HEAD^1 # this would point to the HEAD commit's first parent. The commit we were on before the merge.
HEAD^2 # this would point to the HEAD commit's second parent. The commit we merged in.
master^1 # The 1st parent commit of the tip of the master branch
ce70821^2 # The 2nd parent (note: not grandparent) commit of the commit sha ce70821
```

We can also chain these operators, something like this:

```
HEAD~3^2
```

> This would point to the commit that would be the second parent of the commit that is 3 commits behind the `HEAD`

## [git reset](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) (15/70)

> Video 2 => 7:19

The following command could have disastrous effects on our `HEAD`'s history, the branch HEAD is on. Be very careful when resetting. In most cases, it rewrites history, when it's not it's resetting your working directory. Either way we stand to lose some change we've made. Definitely do not reset a branch's history that has been shared.

`git reset` is a versatile tool for undoing changes. It is easiest to think about reset and its different flags in how it changes and modifies the [three trees of git](https://github.com/andrewsunglaekim/advanced_git_workshop#the-three-trees-515).

> In the following snippets instead of sha's, we will use letters to represent our commit history. `A` being the first commit in the history and `D` being the last commit in the history. There are four commits in this order: `A`, `B`, `C`, `D`. HEAD is currently on commit `D`.

#### resetting the `--soft` way.
In all resets, the commit history(3rd tree) is always changed to reflect the commit passed in.

```
$ git reset --soft B
```

This will set `HEAD` and current branch reference to `B`. Whether we pass in `--soft`, `--mixed`, or `--hard`, it will always change the commit history in this way.

When using the `--soft` flag. The diff between `B` and `D` will be staged for commit to reflect a commit that would encompass the changes from `B` to `D`. In other words the "first tree"(working directory) and "second tree"(the index) has those differences. The `--soft` really just "resets" the commit history(3rd tree).

We might want to do a soft reset when we like the commits we've made, but we want to tack on a bit more to it, kind of like an amend to a commit. Also, we just learned how to squash in git!

> Can also squash with interactive rebasing and squash merging

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

## [Git Reset - You do](https://github.com/andrewsunglaekim/advanced_git_workshop_ex#exercise-2) (5/75)

Now that we've identified the bug in our application. Let's do a hard reset on our application to the commit previous to the bad commit specified in the `bisect` portion.

Hint use `~` against the bad commit sha to find that commit.

## [git reflog](https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog)(10/85)

> video 3 => 00:30

With all these dangerous things we can do in git, surely there must be some way for us to backtrack on disastrous commands. There is! Enter the reference log.

The default expiration time for reflog entries is 90 days. So it's good to fix any immediate mistakes with the reference log, but certainly isn't going to be a possible solution if we've waited too long.

#### references

References, the namesake of `reflog` more commonly known as branches, point to a commit. Quite literally, there is a file with the branch's name in the `.git` directory that has a commit sha of the the commit it is pointing to. Go into any repository with a master branch and run this command:

```bash
$ cat .git/refs/heads/master
```

Then compare that with the tip of `master`'s git log. All this to say, branches point to a commit.

#### `reflog`

Git tracks these branches in a place that isn't ... the branches. It's the `reflog`. We can think of the `reflog` as a chronological history of everything we've done in our local repo. Reference logs record when the tips of branches and other references were updated in the local repository. Reflogs are useful in various Git commands, to specify the value of a reference for a commit that isn't able to be accessed through `git log`.

The most generic `reflog` is simply:

```bash
$ git reflog
```

What we'll see is the default reference log for the `HEAD`. Any change `HEAD` makes, `reflog` will list it out. Each line contains:

- abbreviated commit sha
- a [gitrevision](https://www.git-scm.com/docs/gitrevisions)
- action type(eg. commit, reset, checkout, etc.)
- commit message or description

Often times parsing the default `reflog` can be pretty terse and hard to reason about. There are lot's of commands, arguments and flags that can be passed to the `reflog` command to help us out with that.

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
$ git reflog master@{1.day.1.hour.ago}
```

The above command will output a reference log for master that starts with any changes made 25 hours prior to current time.

We can pass in steps as well like `HEAD@{2}`. And this would be the reference log for all changes prior to 2 changes from the current state of `HEAD`.

> One thing about the `reflog` to note is that reference logs are local. That is to say our reference log and our teammates reference log of the same project will be disparate.

#### Leveraging pointers or commit sha's to rewrite history.
The reflog gives us everything we would want in terms of rewriting history to historical references. We can use either the sha or pointer of a `reflog` entry.

Examples:
Say we have a `reflog` for master that looks something like this:

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
$ git reset --soft master@{2}
```

> these "pointers" can be used in other commands to. With something like `git diff`, we could examine the differences in our branch from the current state to a time interval we specify.

## [Git Reflog - You do](https://github.com/andrewsunglaekim/advanced_git_workshop_ex#exercise-3) (5/90)

OH. NO. We lost some pretty crucial features when we did that last reset. We need to get that code back.

#### Directions
1. Use `git reflog` to find the gitrevision(eg. `HEAD@{2}` or `master@{1}`) or commit sha that you need to get back to prior to the reset done earlier in the workshop.  
2. Once on that commit, checkout to a new branch(call it whatever you'd like)

> At this point, you're browser should be red again.

## [Git revert](https://www.atlassian.com/git/tutorials/undoing-changes/git-revert)(5/95)

> video 4 => 00:32

Another way to "change history" is by not changing a commit but instead adding a new commit using `git revert`. `git revert` takes a specified commit and rolls back the changes made from that commit. Then has us stage changes to continue the revert. `git revert` simply creates a new commit that is the opposite of an existing commit.:

```
$ git revert <some-git-sha>

# might actually look something like this:
$ git revert 67f68c
```

If at this point we have a merge conflict, we will resolve it then stage the fixed changes then run:

```
$ git revert --continue
```

If there wasn't a merge conflict, it will open our text editor for the commit message for the revert.

We can actually revert a series of commits; however, each of these commits will have its own commit to revert.

```
$ git revert <beginning-git-sha>..<final-git-sha>

# might actually look something like this:
$ git revert 67f68c..186ecd
```

This would revert every commit inclusively between `67f68c` and `186ecd`.

> We need to make sure that the commits passed in are of the same history and the first argument proceeds the second in the history.

## Git revert - You do (5/100)

You're back to square 1, everything is red still. Fortunately you still have the commit sha that introduces the bug. You do still have that, right?

#### Directions
1. On the branch you created earlier(post reflog), revert the bad commit from the bisection in the first exercise.
2. Inspect the `index.html` in the browser.

## [Git Merge](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) (5/105)

> video 5 => 00:55

When working with teams on a project, there will often be times where we need to pull changes from a remote repository. We have two main ways to integrate changes from the upstream. One most of us are familiar with is `git merge` the other not as well known method is `git rebase`.

We can use `git merge` in order to combine two branches. The branch that we currently are on is the branch that gets merged into, and the argument to `git merge` is the branch we are trying to merge in.

> With regard to the `^` symbol, `^1` would be the branch we are on, and `^2` would be the branch that is being merged in.

When `git merge` is executed, git creates a new commit that represents the "change" of merging two branches together. This is generally a good thing when used "correctly". However, there is no real "correct" way, and every team/project needs to decide what strategy works for them. Just know that merging does create a commit when we start to talk about merging versus rebasing.

## [Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) (5/110)

> video 5 => 02:45

Similar to when we merge, [`git rebase`](https://git-scm.com/docs/git-rebase) is another way we can grab changes from the upstream. Merge is always a forward moving change record, an additional commit. Alternatively, rebase "rewrites" history.[`git rebase`](https://git-scm.com/docs/git-rebase) reapplies commits on top of another base tip.

[`git rebase`](https://git-scm.com/docs/git-rebase) finds the common ancestor between the branch that we want to rebase and the branch we're rebasing on to. It then "removes" the commits of the rebasing branch until that common ancestor. Then it moves the branch's(the one that's rebasing) tip to the tip of the branch we are rebasing on to. Then it reapplies the same commits that were removed on that new tip.

> The reapplied commits are actually brand new commits with different pointers (git sha's).

To rebase, simply run:

```
$ git rebase <branch-rebasing-onto> <branch-being-rebased>

# OR if you are on the branch that you want to rebase on a new tip
$ git rebase <branch-rebasing-onto>
```

An example of what this might actually look like:

```
$ git rebase master feature

# OR
$ git checkout feature
$ git rebase master
```

If it goes smoothly, we won't even have to do anything. All our changes will be intact on the new base and we can continue to work. If it doesn't go smoothly ...

#### Merge conflicts (5/115)
Just like with merges, git's rebase doesn't know what to do if the two branches edit the same file. It will create a merge conflict in a rebase as well.

We'll fix the conflict like we normally would in a merge.

Stage the changes of that merge,

but **DO NOT** commit. Instead:

```
$ git rebase --continue
```

> If we accidentally commit, we can run `$ git reset --soft HEAD^`. Hmm, What would that do?

If at any point we want to opt out of the rebase entirely, we can always run:

```
$ git rebase --abort
```

We'll have to do this for potentially every commit that is being replayed.

Throughout this whole process, we have never made a commit documenting that we were integrating changes from the upstream. It is as if we had written off of this new base the whole time. Rebasing reduces the noise of merge commits in your git history.

As nice as rebasing is, there are some serious dangers to it. Remember, we are rewriting history. Any time we rewrite history, we should be cautious.

Imagine a scenario where we are working on a feature branch and someone else is assigned to that feature as well. We both have branched off from the feature branch and working on things to make this feature work. Then a third person unknowingly comes along and rebases the feature branch onto master to integrate some changes from a different feature then force pushes that rebase to the remote. Now we have no way of reconciling our changes against the new history and git gets really confused.

The golden rule of rebasing is:

**Don't rebase things that exist outside your(local) repository**.

If we violate this rule, it's only because no one else is using our pushed code that we'd like to rebase.

## Merge vs Rebase

> video 5 => 10:04

So, which is better? Depends on who we ask. There are many differing opinions on this. One perspective is that the repository's commit history is a record of what actually happened. That is to say, if we are grabbing changes from upstream(pulling), version control should know about this merge happening and document it always.

Another perspective is that we want to catalog code changes not meta data about how we are versioning.

If we find ourselves asking if we should be rebasing or merging, the answer lies in understanding how rebasing and merging effect git history and then determining a course of action.

In general the way to get the best of both worlds is to rebase local changes we’ve made but haven’t shared yet before we push them in order to clean up history, but never rebase things we've pushed already. Then merge in rebased feature branches into our master branch or whichever branch is our "clean" one.

## Force Push(5/120)

> video 3 => 13:04

This is a scary one for sure.

First off, if we are about to force push to a remote branch and someone else is also using that remote branch. We **DO NOT** do it.

There are some situations where we will want to force push. More often than not, it's because we've already pushed a remote branch but we're rebasing our branch locally to integrate some changes from the upstream and we want to push that rebased branch to the remote. If we've already pushed the non rebased version, we need to force push the rebased version.

In a force push, we are telling the remote branch to stop pointing at its current commit and instead point to the same commit that our local HEAD is pointing to.

In order to force push, we need to be on the branch reference we'd like to push then run:

```
$ git checkout <branch you want to push>
$ git push -f <remote> <branch>
```

This can have pretty disastrous effect if someone else is working off of that branch as they potentially couldn't reconcile changes against it anymore.

In short, be very cognizant when leveraging a force push. And if we're unsure, it's better to ask someone.

## Communication (5/125)

> video 3 => 14:12

One of the most important tools in git team workflows is not any one command or git interface. It is communication. There aren't any tools or commands that can reduce the amount of merge conflicts. The only thing that can help on that end is good team communication.

If we can have conversations with our team members about which features are being implemented or refactored, we can reduce the amount and complexity of merge conflicts.

If git isn't a consideration on our team when establishing who is working on what, then we should find a way to make it a priority. Whether using a merging or rebasing strategy, communication is paramount in reducing problems that could be introduced with git(merge conflicts). Simply by communicating and avoiding parallel complex implementations of features that touch the same files, we can significantly reduce the amount of overhead and technical debt caused by issues with git.

## Closing (2/127)
Don't be afraid of git. Anything we do can quite literally be undone(within 90 days). Harness its power and become a good git practitioner to see the benefits of leveraging a tool in more ways then just glorified saving.


# Bonus helpful commands that don't really fit into the flow of the lesson plan!
## [git stash](https://git-scm.com/book/en/v1/Git-Tools-Stashing)

There are times when we have dirty working directory and then have to do something in git in which we'd rather not have to deal with modified changes. One solution is to commit your work. However, what if the changes you made don't warrant a commit and you want to continue to work on this feature and commit these changes later?

Enter git stash. Stashing will store the changes in a "stash" on the git repository. Specifically in `./.git/refs/stash`.

In order to stash something, we simply run the following against our dirty directory:

```
$ git stash
```

In order to see all of our various stashes we can run this command:

```
$ git stash list
```

The output of this might look something like this:

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```

The previous output would happen if we had stashed 3 separate times. In order to "re dirty" our working directory we run:

```
# will apply the most recently stashed changes
$ git stash apply

# or if you want to specify a different stash on the list pass in a git revision
# if using the above snippets stash list then changing to 'c264051 Revert "added file_size"' would be:
$ git stash apply stash@{1}
```

## [git commit --amend](https://www.atlassian.com/git/tutorials/rewriting-history)
There are times we make a commit a bit prematurely. Maybe we forgot to remove our `console.log()`'s from our feature branch or we don't feel great about the wording of the last commit message.

We can use `git commit --amend` to make these changes. It lets us combine staged changes with the previous commit instead of creating an entirely new commit.

To amend a commit message:

```
$ git commit --amend -m "updated commit message"
```

Running this command when there is nothing staged lets you edit the previous commit’s message without altering its snapshot. If we do want to add some changes. Simply add them to the index(staging area) and run one of the following

```
$ git add someFileToBeCommitted.txt

# if we don't want to edit the original commit message
$ git commit --amend --no-edit

# if we do want to edit the commit message
$ git commit --amend -m "updated commit message"
```

**DO NOT** amend public commits for the same reasons we do not rebase public commits. Amending creates a brand new commit.

> Also we can also do `$ git reset --soft HEAD^` to do the same things.

## [the `-p` flag or ("patch-mode")](https://stackoverflow.com/questions/10605405/what-does-each-of-the-y-n-q-a-d-k-j-j-g-e-stand-for-in-context-of-git-p)
For many commands in git we can run them in "patch mode". Patch mode will also us to view chunks of changes and allow us to decide what do with them. Some commands that can be run in patch mode are but not limited to: `add`, `checkout`, `reset`, `stash`.

To stage things in patch mode we might run something like this:

```
$ git add someFileName -p
```

It will show us a diff of some chunk of code ready to be staged and prompt us with what we want to do with it.

```
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk nor any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk nor any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

The first 5 are the most helpful. When we enter a command it will then go to the next chunk until the process is finished.
