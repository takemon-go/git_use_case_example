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
$ mkdir -p ~/repos/repo1
$ cd ~/repos/repo1/
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

## stage the modified file

```
$ git add README.md
```

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   README.md
```

```
$ git diff
$ git diff --cached
$ git diff HEAD
```

## edit the file

```
$ sed -i "" "s/\[email\]/your email address here/g" README.md
```

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

```
$ git diff
$ git diff --cached
$ git diff HEAD
```

```
$ git add README.md
$ git commit -m "modified user name and email."
```

or

```
$ git commit -m "modified user name and email." README.md
```

or

```
$ git commit -am "modified user name and email."
```

* `git commit -a` is shorthand for `git add -u; git commit`.

```
$ git show
```

## replace the latest commit

```
$ git commit --amend
```

# create shared repository

```
$ mkdir -p ~/repos/repo0
$ pushd ~/repos/repo0/
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
$ git remote add origin ~/repos/repo0
```

```
$ git remote -v
```

```
$ git push --set-upstream origin master
```

# clone repository

```
$ cd ~/repos/
```

```
$ git clone repo0 repo2
```

# collaborate with other repositories

```
$ cd ~/repos/repo2/
```

modify `README.md`, then

```
~/repos/repo2 (master)
$ git commit -F- README.md << __EOF__
modified README, added contents about commit, shared repository, clone.

* commit file to repository
* create shared repository
* clone repository
__EOF__
```

```
~/repos/repo2 (master)
$ git push
```

```
$ cd ~/repos/repo1/
```

modify `README.md`, then

```
~/repos/repo1 (master)
$ git commit -m "modified README, added contents about collaboration with other repositories." README.md
```

`git push` is rejected in this case.

```
~/repos/repo1 (master)
$ git push
To ~/repos/repo0
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to '~/repos/repo0'
```

```
~/repos/repo1 (master)
$ git fetch
```

```
~/repos/repo1 (master)
$ git log origin/master
```

```
~/repos/repo1 (master)
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
~/repos/repo1 (master|REBASE 1/1)
$ git add README.md
$ git rebase --continue
```

* `git pull` is short hand for `git fetch` followed by `git merge`.
* `git pull --rebase` is short hand for `git fetch` followed by `git rebase`.

```
~/repos/repo1 (master)
$ git push
```

# branch and merge

```
~/repos/repo1 (master)
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
~/repos/repo1 (add_topic_of_branch_and_merge)
$ git commit -m "modified README, added topic of branch and merge." README.md
```

if you need to push this branch:

```
~/repos/repo1 (add_topic_of_branch_and_merge)
$ git push origin add_topic_of_branch_and_merge
```

```
~/repos/repo1 (add_topic_of_branch_and_merge)
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
```

```
~/repos/repo1 (master)
$ git log --graph --oneline --branches
```

show branch list:

```
~/repos/repo1 (master)
$ git branch
$ git branch -a
```

merge the topic branch into master branch:

```
~/repos/repo1 (master)
$ git merge --no-ff add_topic_of_branch_and_merge
```

```
~/repos/repo1 (master)
$ git log --graph --oneline --branches
```

```
~/repos/repo1 (master)
$ git push
```

if the topic branch is no longer necessary:

```
~/repos/repo1 (master)
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
* log --grep, -S
* tag
* stash
* rebase -i
* cherry-pick
* blame
* bisect
* various other useful options of each commands
* workflow management (Git Flow, Github Flow, ...)
* :
