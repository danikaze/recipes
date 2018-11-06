# Git

## Setup

At least, you need to set your user and email (remove the `--global` part if you just want to set the user for the current repository):

```sh
git config --global user.name "John Doe"
git config --global user.email "johndoe@rakuten.com"
```

## List of common aliases

Displayed here as commands to configure your git client:

```sh
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.cm commit
git config --global alias.ri "rebase --interactive"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.undo-commit "reset --soft HEAD~"
git config --global alias.aliases "config --get-regexp ^alias\."
```

## Create [and publish] a branch to remote

Create the new branch:

```sh
git co -b <NEW_LOCAL_BRANCH> <PARENT_BRANCH>
```

`PARENT_BRANCH` can be the name of a local branch, or a remote like this `origin/BRANCH_NAME`.

When changes are commited, publish it to remote:

```sh
git push -u origin <REMOTE_BRANCH>
```

## Tags

### Create a local tag

Tag the last commit of the current branch:

```sh
git tag <TAG_NAME>
```

Or another commit
```sh
git tag <TAG_NAME> <COMMIT_HASH>
```

### Push the tag to remote

```sh
git push --tags
```

### Update a tag

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

## Renaming a remote branch

```sh
git push origin origin/<OLD>:refs/heads/<NEW> :<OLD>

# If we have a local_branch associated with branch1,
# we need to associate it with branch2,
# so the new pushes are uploaded to the correct branch.
git checkout <LOCAL>
git branch -u origin/<NEW>
```
