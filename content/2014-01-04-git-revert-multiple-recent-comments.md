---
date: 2014-01-04
tags: git
---
Git - how to revert multiple recent commits
============================================

Let's assume we have a history like this:

```bash
G1 - G2 - G3 - B1 - B2 - B3
```

Where G1-G3 are 'good' commits and B1-B3 are 'bad' commits and we
want to revert them.
<!-- more -->

If changes are not pushed to the server yet then the easy way is
to reset the state to previous commit with `reset`:

```bash
$ git reset --hard HEAD~3
```

Here we can refer to `B3` as `HEAD`, `B2` is `HEAD~1`, `B1` is `HEAD~2`.
This way the last good commit `G3` is `HEAD~3`:

```bash
G1 - G2 - G3 - B1 - B2 - B3
           \   \     \    \-- HEAD
            \   \     \------ HEAD~1
             \   \----------- HEAD~2
              \-------------- HEAD~3
```

But if changes are pushed already then it is better to use `revert`.
And here is how to do this for multiple commits:

```bash
$ git revert --no-commit HEAD~2^..HEAD
```

Or:

```bash
$ git revert --no-commit HEAD~3..HEAD
```

We need to revert a range of revisions from B1 to B3.
The range specified with two dots like `<rev1>..<rev2>` includes only
commits reachable from `<rev2>`, but not reachable from `<rev1>` (see `man -7 gitrevisions`).

Since we need to include B1 (represented by HEAD~2) we use HEAD~2^ (its parent) or HEAD~3 (also parent of HEAD~2).
The `HEAD~2^` syntax is more convenient if commit SHAs are used to name commits.

The `--no-commit` option tells git to do the revert, but do not
commit it automatically.

So now we can review the repository state and commit it.
After that we will get the history like this:

```bash
G1 - G2 - G3 - B1 - B2 - B3 - R`
```

Where `R'` is a revert commit which will return repository state to the commit `G3`.
Run git diff to check this (output should be empty):

```bash
$ git diff HEAD~4 HEAD
```

Another way to run revert is to specify commits one by one from newest to oldest:

```bash
$ git revert --no-commit HEAD HEAD~1 HEAD~2
```

In this case there is no need to specify HEAD~3 since it is a good commit we do not want to revert.


Links
============================================
[Stackoverflow: Revert multiple git commits](http://stackoverflow.com/questions/1463340/revert-multiple-git-commits)

[Stackoverflow: Revert a range of commits in git](http://stackoverflow.com/questions/4991594/revert-a-range-of-commits-in-git)

[Stackoverflow: Git diff .. ? What's the difference between having .. and no dots](http://stackoverflow.com/questions/7251477/git-diff-whats-the-difference-between-having-and-no-dots)

[What's the difference between HEAD^ and HEAD~ in Git?](http://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git)
