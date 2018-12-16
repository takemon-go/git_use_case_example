# git - a use case example

## references

* <https://git-scm.com/docs/>
* <http://files.zeroturnaround.com/pdf/zt_git_cheat_sheet.pdf>

# environment

* macOS 10.14.2
* git 2.19.0

# config

```
$ git config --global core.autocrlf false
$ git config --global core.quotepath false
$ git config --global core.longpaths true
```

## user

```
$ git config --global user.name "[name]"
$ git config --global user.email "[email]"
```

## editor

```
$ script='export PATH="/Applications/Visual Studio Code.app/Contents/Resources/app/bin":$PATH'
$ eval $script
$ echo $script >> ~/.bash_profile
```

```
$ git config --global core.editor "code --wait"
```

# create repository

## init

```
$ mkdir -p /tmp/repo1
$ cd /tmp/repo1/
```

```
$ git init
```

## first commit

```
$ git commit -m "(empty)" --allow-empty
```

## show log

```
$ git log
```

# commit file to repository

## create a file

```
$ curl -fsSL https://raw.githubusercontent.com/takemon-go/git_use_case_example/master/sample.md > README.md
```

## show status

```
$ git status
```

## stage a new file

```
$ git add README.md
```

* `git add -A` stages all changes.
* `git add -u` stages modifications and deletions, without new files.
* `git add .` stages new files and modifications in the current directory and its sub directories, without deletions.

```
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   README.md
```

## commit

```
$ git commit
```

```
$ git status
On branch master
nothing to commit, working tree clean
```

## edit the file

```
$ sed -i "" "s/\[name\]/your name here/g" README.md
```

```
$ git status
```

## show the changes

### between the index and the working tree.

```
$ git diff
```

### between the latest commit and the index.

```
$ git diff --cached
```

or

```
$ git diff --staged
```

`--staged` is a synonym of `--cached`.

### between the latest commit and the working tree.

```
$ git diff HEAD
```

## stage the file

```
$ git add README.md
```

```
$ git status
$ git diff
$ git diff --cached
$ git diff HEAD
```

## edit the file

```
$ sed -i "" "s/\[email\]/your email here/g" README.md
```

```
$ git status
$ git diff
$ git diff --cached
$ git diff HEAD
```

```
$ git add README.md
$ git commit -m "modified user name and email."
```

```
$ git log
```

## replace the latest commit

```
$ git commit --amend
```

# create shared repository

```
$ mkdir -p /tmp/repo0
$ pushd /tmp/repo0/
```

```
$ git init --bare
```

```
$ popd
```

```
$ git remote -v
```

```
$ git remote add origin /tmp/repo0
```

```
$ git remote -v
```

```
$ git push --set-upstream origin master
```

# clone repository

```
$ cd /tmp/
```

```
$ git clone repo0 repo2
```

# collaborate with other repositories

```
$ cd /tmp/repo2/
```

modify `README.md`, then

```
/tmp/repo2 (master)
$ git commit -F- README.md << __EOF__
modified README, added contents about commit, shared repository, clone.

* commit file to repository
* create shared repository
* clone repository
__EOF__
```

```
/tmp/repo2 (master)
$ git push
```

```
$ cd /tmp/repo1/
```

modify `README.md`, then

```
/tmp/repo1 (master)
$ git commit -m "modified README, added contents about collaboration with other repositories." README.md
```

`git push` is rejected in this case.

```
/tmp/repo1 (master)
$ git push
To /tmp/repo0
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to '/tmp/repo0'
```

```
/tmp/repo1 (master)
$ git fetch
```

```
/tmp/repo1 (master)
$ git log origin/master
```

```
/tmp/repo1 (master)
$ git rebase
    :
CONFLICT (content): Merge conflict in README.md
error: Failed to merge in the changes.
Patch failed at 0001 modified README, added contents about collaboration with other repositories.
    :
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

edit `README.md` to merge conflict.

    <<<<<<< HEAD
    (changes in shared repository)
    =======
    (changes in local repository)
    >>>>>>> modified README, added contents about collaboration with other repositories.

```
/tmp/repo1 (master|REBASE 1/1)
$ git add README.md
$ git rebase --continue
```

* `git pull` is short hand for `git fetch` followed by `git merge`.
* `git pull --rebase` is short hand for `git fetch` followed by `git rebase`.

```
/tmp/repo1 (master)
$ git push
```

# branch and merge

```
/tmp/repo1 (master)
$ git checkout -b add_topic_of_branch_and_merge
Switched to a new branch 'add_topic_of_branch_and_merge'
```

This is shorthand for:

```
$ git branch add_topic_of_branch_and_merge
$ git checkout add_topic_of_branch_and_merge
```

modify `README.md`, then

```
/tmp/repo1 (add_topic_of_branch_and_merge)
$ git commit -m "modified README, added topic of branch and merge." README.md
```

if you need to push this branch:

```
/tmp/repo1 (add_topic_of_branch_and_merge)
$ git push origin add_topic_of_branch_and_merge
```

```
/tmp/repo1 (add_topic_of_branch_and_merge)
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
```

```
/tmp/repo1 (master)
$ git log --graph --oneline --branches
```

show branch list:

```
/tmp/repo1 (master)
$ git branch
$ git branch -a
```

merge the topic branch into master branch:

```
/tmp/repo1 (master)
$ git merge --no-ff add_topic_of_branch_and_merge
```

```
/tmp/repo1 (master)
$ git log --graph --oneline --branches
```

```
/tmp/repo1 (master)
$ git push
```

if the topic branch is no longer necessary:

```
/tmp/repo1 (master)
$ git branch -d add_topic_of_branch_and_merge
$ git push origin :add_topic_of_branch_and_merge
```

# revert, reset, reflog

`git revert` reverts some existing commits.

```
$ git revert <commit>
```

`git reset` resets current HEAD to the specified state.

```
$ git log --oneline
$ git reset HEAD^
$ git log --oneline
$ git reset HEAD~2
$ git log --oneline
```

```
$ git reflog
$ git reset <commit>
```

to discard changes in working directory:

```
$ git checkout -- <files>
```

reset options:

* `--mixed` resets the index but not the working tree. This is the default action.
* `--hard` resets the index and the working tree.
* `--soft` does not touch the index file or the working tree at all.

# other topics

* .gitignore
* log message format
* log --grep, -S
* tag
* stash
* rebase -i
* cherry-pick
* blame
* bisect
* workflow management (Git Flow, Github Flow, ...)
* :
