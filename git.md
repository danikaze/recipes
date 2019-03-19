# Git

* [Setup](#setup)
* [List of common aliases](#aliases)
* [Create [and publish] a branch to remote](#create-branch)
* [Tags](#tags)
  * [Create a local tag](#create-tags)
  * [Push the tag to remote](#push-tags)
  * [Update a tag](#update-tags)
* [Renaming a remote branch](#renaming-remote-branch)
* [Removing a remote branch (or tag)](#removing-remote-branch)
* [Rebasing](#rebase)
  * [Rebase on top of master](#rebase-master)
  * [Rebase --interactive](#rebase-interactive)
  * [Rebase example](#rebase-example)
  * [Fixing authors in history](#fixing-author)


## <a name="setup"></a>Setup

At least, you need to set your user and email (remove the `--global` part if you just want to set the user for the current repository):

```sh
git config --global user.name "danikaze"
git config --global user.email "danikaze@gmail.com"
```

## <a name="aliases"></a>List of common aliases

Displayed here as commands to configure your git client:

```sh
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.cm commit
git config --global alias.ri "rebase --interactive"
git config --global alias.l "log --pretty=format:'%C(yellow)%h %C(red)%ad %C(cyan)%an %C(green)%d %C(reset)%s' --date=short -n30"
git config --global alias.ls "log --pretty=format:'%C(yellow)%h %C(red)%ad %C(cyan)%an %C(green)%d %C(reset)%s' --date=short --stat -n30"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.undo-commit "reset --soft HEAD~"
git config --global alias.aliases "config --get-regexp ^alias\."
```

_Extra: [Git log format string cheatsheet](https://devhints.io/git-log-format)_

## <a name="create-branch"></a>Create [and publish] a branch to remote

Create the new branch:

```sh
git co -b <NEW_LOCAL_BRANCH> <PARENT_BRANCH>
```

`PARENT_BRANCH` can be the name of a local branch, or a remote like this `origin/BRANCH_NAME`.

When changes are commited, publish it to remote:

```sh
git push -u origin <REMOTE_BRANCH>
```

## <a name="tags"></a>Tags

### <a name="create-tags"></a>Create a local tag

Tag the last commit of the current branch:

```sh
git tag <TAG_NAME>
```

Or another commit
```sh
git tag <TAG_NAME> <COMMIT_HASH>
```

### <a name="push-tags"></a>Push the tag to remote

```sh
git push --tags
```

### <a name="update-tags"></a>Update a tag

Actually, the tag is removed from local, then from remote, and then recreated.

```sh
# Remove the local tag
git tag -d <TAG_NAME>
# Remove the remove tag
git push origin :refs/tags/<TAG_NAME>
# Recreate the local tag
git tag <TAG_NAME>
# Push it to remote
git push --tags
```

## <a name="renaming-remote-branch"></a>Renaming a remote branch

```sh
git push origin origin/<OLD>:refs/heads/<NEW> :<OLD>

# If we have a local_branch associated with OLD branch,
# we need to associate it with NEW branch,
# so the new pushes are uploaded to the correct branch.
git checkout <LOCAL>
git branch -u origin/<NEW>
```

## <a name="removing-remote-branch"></a>Removing a remote branch (or tag)

```sh
# This is the same as saying: copy an EMPTY branch into :<BRANCH_NAME>
# and it's the same with tags
git push origin :<BRANCH_NAME>
```

## <a name="rebase"></a>Rebase

Contrarily to `merge`, `rebase` takes your changes and applies them after any other commit we are rebasing against.

### <a name="rebase-master"></a>Rebase on top of master

Whether we fork any library or branch any project, the original code (from now, `master`) might be updated like this:

```
     E---F---G (feature)
    /
A---B---C---D (origin/master)
```

When we want to introduce (merge) our changes (`feature` branch) into `master`, it should be taking into account the latest changes already applied (`C` and `D`).

This is the use case of `rebase`: apply our changes (`E`, `F` and `G`) one by one after `C` and `D` like this:

```
A---B---C---D---E'--F'--G' (origin/master)
```

```sh
# get the latest changes from remote
git fetch origin
# reaply our changes
git rebase origin/master

# if there are conflicts...
git rebase --continue

# when everything is applied and the rebase finishes
git push
```

### <a name="rebase-interactive"></a>Rebase --interactive

When rebasing interactively, there are different options (`presfx`).

* `p` or `pick` is the default option. It just gets that commit and apply it as usual.
* `r` or `reword` does the same, but lets you change the commit message. Don't rewrite the message here, because when saving the to-do-list, git will apply all the picked commits and open the editor again with the commit message you want to reword, as if you were commiting it again. Now, you shall change the message.
* `e` or `edit` stops after applying the commit, so you can run other commands as `git commit --amend`, etc. (see fixing the author in the history)
* `s` or `squash`. It put the `s`_'ed_ commit together with the previous one (the one above). The commit messages get concatenated, one after the other.
* `f` or `fixup` does the same as `squash`, but discarding the commit message.

#### <a name="rebase-example"></a>Rebase example

Let's say we have this history:

```
69218f8 (HEAD) Fixed B tests
225e448 Added tests for B
f90b697 Added tests for feature A
4bba91c Added B
c4e3111 Fix linting
091a64d Small bug fix
7e5ed88 Added new feature A
75f1639 Fixed typos
2ce4d0d Added new file
350bf43 Hello world 🎉
```

And we want a clean git history by doing:
* Merge `75f1639` with `2ce4d0d` because the _fixed typos_ should not be a new commit, the new file should be there with no typos.
* By the same reason, merge `c4e3111`, `091a64d` and `7e5ed88` to unify everything of adding the _new feature A_ into one commit. This way would be much easier to revert in case of removing it, or even just to read what was needed to add it.
* `f90b697` should be just after it (or even in the same one, but we are going to keep two different commits for the feature and its tests).
* `4bba91c` and `225e448` should be the two last commits, but we are going to fix their messages, changing `B` by `feature B` for consistency. Also, `69218f8` should be merged into `225e448`.

So we are going to run `git rebase --interactive` (or just `git ri` if you are using the provided aliases from above).

_We have a very small history here, but usually you want only from HEAD to the first commit to use as a base for the rebase, in this case `2ce4d0d`_

We can run then, `git ri 2ce4d0d^` (or `git ri 350bf43` is the same. Just make sure you don't include commits that aren't from your changes, or they would be applied twice), and then, we will have a screen like this:

```
pick 2ce4d0d Added new file
pick 75f1639 Fixed typos
pick 7e5ed88 Added new feature A
pick 091a64d Small bug fix
pick c4e3111 Fix linting
pick 4bba91c Added B
pick f90b697 Added tests for feature A
pick 225e448 Added tests for B
pick 69218f8 Fixed B tests

# Rebase 350bf43..69218f8 onto 350bf43 (9 commands)
#
# Commands:
# ...
```

We can see that the oldest commit is the top one, and the default action for all commits is `pick`. Now we just need to change it so it makes what we want, which would be something like this (the added comments are not needed):

```
pick 2ce4d0d Added new file
fixup 75f1639 Fixed typos
pick 7e5ed88 Added new feature A
fixup 091a64d Small bug fix
fixup c4e3111 Fix linting
pick f90b697 Added tests for feature A
reword 4bba91c Added B
reword 225e448 Added tests for B
fixup 69218f8 Fixed B tests
```

Basically we are just editing a **todo** file with the commands we want git to execute. When we save and exit, git will start applying the actions as specified from top to bottom in that order. That means several things:

* We can reorder the commits (as we did with `f90b697`)
* If we remove one line, that commit will not be applied (however, if we save a blank document, the rebase will be cancelled)

Note that overwriting history like this might cause your branch to diverge with the origin one like this:

```
On branch XXX
Your branch and 'origin/XXX' have diverged,
and have 35 and 94 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
```

Instead of following git indications with a `git pull`, to apply your changes you will need to do it with the `--force` flag:

```
git push --force
```

This means that **our original commits will be replaced with new ones**. This is safe if you are the only one working in this branch, but if anyone is based their work on any of this commits, he will need to rebase/merge with the consecuent posibility of facing **conflicts** on their code.

As long as everyone knows what's happening it should be ok (so this is more a communication and workflow problem that a technical one). Just make sure you have this clear ;)

#### <a name="fixing-author"></a>Fixing authors in history

This is another fix we can do with `rebase --interactive`. And it happens (to me at least) more than desired, when I clone the repository in a different machine/location and don't [set the author](#setup) with the one I'm using in that repository.

Basically, we just need `git rebase --interactive` and set the commit(s) we want to fix to `edit` mode.

When applying the commands, git will stop at each one so we can run this:

```
# The --no-edit avoids opening the editor to modify the commit message (since we just want to change the author)
git commit --amend --author="danikaze <danikaze@gmail.com>" --no-edit
# Tell git we have finished ammending the commit, and continue with the next one (or finish)
git rebase --continue
```
