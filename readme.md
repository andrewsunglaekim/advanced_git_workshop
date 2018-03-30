# Advanced Git Workshop

## Learning Objectives

- Identify what the HEAD in git is.
- Use `git checkout` to inspect commits or change the file tree.
- Define detached HEAD state
- Use bisection (`git bisect`) to indentify potentially bad commits.
- Determine relationships between commits
- Identify how `~` and `^` characters are used to leverage commit relationships
- Describe the three trees of git
- git merge revsisted
- Reset git history
a

**linked list*

### bisection

This is why small purposeful semantic commits can be really helpful for code maintainability. 

## The three trees
When we think about git, we can boil down a good amount of it's commands with how they influence the three "trees". 
git reset - https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified
- `--hard` is dangerous, because  

git diff - https://stackoverflow.com/questions/1191282/how-to-see-the-changes-between-two-commits-without-commits-in-between

## git merge

You’ll notice the phrase “fast-forward” in that merge. Because the commit C4 pointed to by the branch hotfix you merged in was directly ahead of the commit C2 you’re on, Git simply moves the pointer forward. To phrase that another way, when you try to merge one commit with a commit that can be reached by following the first commit’s history, Git simplifies things by moving the pointer forward because there is no divergent work to merge together — this is called a “fast-forward.”
