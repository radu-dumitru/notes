## Branch Management

| Action | Command |
| ------ | ------- |
| Print all local branches |  `git branch` | 
| Print all remote branches | `git branch -r`
| Print all branches (remote + local) | `git branch -a`
| Go to an existing branch | `git checkout <branch-name>`
| Create a new branch and switch to it | `git checkout -b <branch-name>`
| Delete a local branch <br/>`-d` the branch must be fully merged in its upstream branch, or in HEAD if no upstream was set | `git branch -d <branch-name>` | 
| Delete a local branch <br/> `-D` stands for `--delete --force`, will delete the branch regardless of its merge status | `git branch -D <branch-name>` |
| Delete a remote branch | `git push <remote-name> --delete <branch-name>`
| List branches merged into master **(specify `-a` or `-r` after `branch` to display local+remote or just remote)** | `git branch --merged master`
| List branches merged into the HEAD **(specify `-a` or `-r` after `branch` to display local+remote or just remote)** | `git branch --merged`
| List branches that have not been merged **(specify `-a` or `-r` after `branch` to display local+remote or just remote)** | `git branch --no-merged`

<br/>

## Tracking Branches

By default, local and remote branches in Git have nothing to do with each other. 
<br/>
However, you can create a connection between 2 branches by telling to the local branch to "track" a remote branch. 

### Advantages of setting tracking connections:

1. Git can inform you if your local branch is behind or ahead of its remote counterpart.

2. Pushing and pulling becomes easier. You can use the shorthand commands: `git pull` and `git push`. 

`git checkout <branch>`
<br/>
Checking out a local branch from a remote-tracking branch automatically creates what is called a “tracking branch” (and the branch it tracks is called an “upstream branch”).

### Publish a local branch for the first time
When you want to publish a local branch to a remote branch for the first time, use the following command: `git push -u <remote> <branch>`

### When you decide at a later point in time
If you want for a local branch to track an existing remote branch, use the following command: `git branch -u <remote>/<branch>`

<br/>

## Get remote changes

| Action | Command |
| ------ | ------- |
| Get the latest updates from origin but don't integrate them | `git fetch`
| Delete refs to branches that don't exist on the remote | `git remote prune origin`
| Before fetching, remove any refs that no longer exist on the remote | `git fetch --prune` or `git fetch -p`
| Get the latest updates of the current branch and integrate them | `git pull <remote> <branch>` <br/> <br/> `git pull` (if tracking is set)
| Display a tree of commits | `git log --graph --all --decorate --oneline`

<br/>

## Publishing you local changes

### No tracking set

`git push <remote> <branch>`

Example: `git push origin master` (push from the local branch named `master` to the remote branch named `master`)

### Tracking set

`git push`

### Pushing commits to another branch

If your local branch has a different name to the remote branch, use the following command:
<br/>
`git push <remote> <local-branch>:<remote-branch>`

## Discarding changes

| Action | Command |
| ------ | ------- |
| Discard local changes to a file | `git checkout -- <file>`
| Discard all changes to all files | `git reset --hard`

## Store credentials

`git config --global credential.helper store`
