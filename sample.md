# git - a use case example

## reference

* <https://git-scm.com/docs/>
* <http://files.zeroturnaround.com/pdf/zt_git_cheat_sheet.pdf>

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
