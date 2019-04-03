# Welcome to GitSteps.com!
This is simply a collection of commands to help you get around [Git](http://git-scm.com/), [GitHub](https://github.com/) and/or [Gerrit](https://code.google.com/p/gerrit/). It's a single page, so your browser's "Find" command is your friend. [Pull requests](https://help.github.com/articles/using-pull-requests) are welcome at the [GitHub repo](https://github.com/JohnRiv/gitsteps).

Except for instances of "HEAD", anything in ALL CAPS should be substituted with the actual value.

## General Git Steps

### Goal: Stage all changes
i.e. get ready to commit all outstanding changes
```
$ git add .
```

### Goal: Save outstanding changes but don't commit them
This is useful if you want to merge in code from the remote repo or work in a different branch than you are currently in
```
$ git stash
```
and then when you're ready to bring those changes back in:
```
$ git stash pop
```

### Goal: I want to just type 'git push' instead of 'git push origin master'
Run this command once from the root of your project:
```
$ git push -u origin master
```

### Goal: Stop this error when trying to pull from a remote origin:
> You asked me to pull without telling me which branch you
> want to merge with, and 'branch.master.merge' in
> your configuration file does not tell me either.

Add this to your .git/config file for your project:
```
[branch "master"]
  remote = origin 
  merge = refs/heads/master
```

### Goal: Search git logs
```
$ git log --grep="search term"
```

### Goal: Blow away all local changes
```
$ git reset --hard HEAD
```

### Goal: Update to latest code from the remote origin
```
$ git pull
```

### Goal: Have 'git pull' do a rebase instead of a merge by default
Add this to your .git/config file for your project:
```
[pull]
  rebase = true
```

### Goal: Get rid of the "Your branch and 'origin/master' have diverged..." status message
If you're really confident your code actually matches what is at the remote origin/master
```
$ git reset --hard origin/master
```

### Goal: Fix a merge conflict by accepting changes
Accept changes on the remote server:
```
$ git checkout --theirs PATH_TO_FILE
```
Accept your changes:
```
$ git checkout --ours PATH_TO_FILE
```

### Goal: Merge in a specific commit from one branch to another
If you're really confident your code actually matches what is at the remote origin/master
```
$ git checkout BRANCH_NAME
$ git cherry-pick SHA_HASH
$ git push origin HEAD:refs/for/BRANCH_NAME
```


### Goal: Stats on number of commits by committer
```
$ git shortlog -sne --after=2013-07-01 --until=today
```

### Goal: Remove Edits Not Staged for Commit (permanently)
First run this to make sure you'll be removing the changes you think you're removing
```
$ git status | grep modified | awk '{print $3}'
```
This run this to actually blow away the changes in those files
```
$ git checkout -- `git status | grep modified | awk '{print$3}'`
```

### Goal: Edit the last commit
```
$ git commit --amend
```

### Goal: Undo the last commit
```
$ git reset --soft HEAD^
```

### Goal: Get rid of the last commit for good
```
$ git reset --hard HEAD~1
```

### Goal: Show remote repo URL:
```
$ git remote show origin
```

### Goal: Stats on number of commits by committer
```
$ git shortlog -sne --after=2013-07-01 --until=today
```

### Goal: Display git details in terminal
Check out [Trevor Menagh's .bashrc](https://github.com/trevmex/dotfiles/blob/master/.bashrc) to see how to have your terminal add details of the current branch, if there are any changes (marked by an "*"), and if you're in the middle of a rebase

### Goal: Use a different email for a specific folder of git projects

in ~/.gitconfig:
```
[includeIf "gitdir:~/path/to/folder/"]
    path = ~/path/to/folder/.gitconfig_include
```

in  ~/path/to/folder/.gitconfig_include:
```
[user]
  email = your.other@email.com
```
Note: the above requires at least git v2.13

## GitHub Steps

### Goal: Move an existing git repo to GitHub:
First create a new repo at GitHub, then:
```
$ git clone SSH_CLONE_URL_FROM_GITHUB_REPO
$ git remote rename origin upstream
$ git remote add origin URL_TO_GITHUB_REPO
$ git push origin master
```

### Goal: Undo a GitHub push:
```
$ git reset --hard SHA_HASH
$ git push -f origin stable  
```

### Goal: Merge in code from originally forked repo:
Short version:
```
$ git fetch upstream
$ git merge upstream/master
$ git push origin master
```
Much more detailed version is available at GitHub Help: [Syncing a fork](https://help.github.com/articles/syncing-a-fork)

## Gerrit Steps

### Goal: I want to just type 'git p' instead of 'git push origin HEAD:refs/for/master'
Add this to your ~/.gitconfig
```
[alias]
    p = push origin HEAD:refs/for/master
```

### Goal: Resolve a Gerrit conflict
```
$ git fetch COMMAND_FROM_GERRIT
$ git fetch
$ git rebase origin/master
$ git status
```
Then go and fix the conflict(s) listed in status as unmerged
```
$ git add FILE_YOU_FIXED
$ git rebase --continue
$ git push origin HEAD:refs/for/master
```
You may want to checkout these steps as well after resolving a conflict:
* Restore your local git repo after reviewing a gerrit changeset
* Get rid of the "Your branch and 'origin/master' have diverged..." status message

### Goal: Restore your local git repo after reviewing a gerrit changeset:
```
$ git checkout master
```

### Goal: Rebase a commit that is dependent on an abandoned change:
```
$ git fetch origin
$ git rebase origin/master -i
$ git commit --amend
$ git push origin HEAD:refs/for/master
```

### Goal: Fix SSL certificate problem error
If you get an error along these lines:
> error: SSL certificate problem, verify that the CA cert is OK. Details:
> error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed while accessing GERRIT_REPO
> fatal: HTTP request failed

Run this from the project root:
```
$ git config http.sslVerify "false"
```
