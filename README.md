# git

## Description
This project is designed to introduce one to the world of version control and collaboration using Git.

## Note
* I'm using bash so I'm able to use 'printf' command. If you are using a different shell use 'echo' or the appropriate command.
* Branch master and main are synonymous i.e github is using main while gitea is still using master.



## Setting Up Git

* Use the command below to install git on Linux
```bash
    $ sudo apt install git-all
```
* Configure Git with your username and email address.
```bash
    $ git config --global user.email "rotieno@gmail.com"
    $ git config --global user.name "rotieno"
```
## Create 'work' directory

*   ```bash
    $ mkdir work
    ```

## Git commits to commit

*   Create a subdirectory named hello within the 'work' directory then create a file named hello.sh inside it. 
* To view the log of the shared repo:
```bash
$ cd work
$ git --git-dir=hello.git log --oneline
```
    
```bash
    $ cd work
    $ mkdir hello
    $ cd hello
    $ echo 'echo "Hello, World"' > hello.sh
```
* Initialize git repo in the hello directory.

```bash
    $ git init
```
*   Check the status

```bash
    $ git status
```
*   Change hello.sh content

```bash
    $ echo '#!/bin/bash' > hello.sh
    $ echo ' ' >> hello.sh 
    $ echo 'echo "Hello, $1"' >> hello.sh
```
*   Stage and commit the changes then check if working tree is clean.

```bash
    $ git add hello.sh
    $ git commit -m "chore: change contents of hello.sh"
    $ git status
```
*   Modify hello.sh and stage it. Make 2 separate commits.
```bash
    $ echo  '#!/bin/bash' > hello.sh
    $ echo ' ' >> hello.sh
    $ echo '# Default is "World"' >> hello.sh
    $ git add hello.sh
    $ git commit -m "chore: add comment in line 3"
```
```bash
    $ echo 'name=${1:-"World"}' >> hello.sh
    $ echo 'echo "Hello, $name"' >> hello.sh
    $ git add hello.sh
    $ git commit -m "chore: add line 4 and 5 changes"
```
## History

* Show history
```bash
    $ git log
```
*  Show one-line history
```bash
    $ git log --oneline
```
*  Controlled entries(show the last 2 entries and commits made within last 5 minutes.)

```bash
    $ git log -n 2
```
```bash
    $ git log --since="5 minutes ago"
```
*   Personalized format (Show logs in a personalized format, including the commit hash, date, message, branch information, and author name, resembling * e4e3645 2023-06-10 | Added a comment (HEAD -> main) [John Doe])

```bash
    $ git log --pretty=format:'* %h %ad | %s (%d) [%an]' --date=format:'%Y-%m-%d'
```

## Check it out

*  Restore First Snapshot
    ```bash
    $ first_commit_hash=$(git rev-list --max-parents=0 HEAD)
    $ git checkout $first_commit_hash
    $ cat hello.sh
    ```
*   Restore Second Recent Snapshot
    ```bash
    $ second_commit_hash=$(git rev-list HEAD | sed -n '2p')
    $ git checkout $second_commit_hash
    $ cat hello.sh
    ```
*   Return to Latest Version
    ```bash
    $ git checkout master
    $ cat hello.sh
    ```
*   Commit the changes
     ```bash
    $ git add "check it out"
    $ git commit -m "chore: add 'check it out' exercise"
    ```
## TAG me

* Referencing Current Version
   ```bash
   $ git tag v1
   ````

* Tagging Previous version
  ```bash
  $ git tag v1-beta HEAD^
  ```

* Navigating Tagged Versions:
 ```bash
 $ git checkout v1
 $ git checkout v1-beta
  ```
*   Listing Tags
```bash
 $ git tag
  ```
*   Commit the changes
     ```bash
    $ git add "TAG me"
    $ git commit -m "chore: add 'TAG me' exercise"
    ```

## Changed your mind?

*  Reverting Changes
```bash
$  printf '#!/bin/bash\n\n# This is a bad comment. We want to revert it.\nname=${1:-"World"}\n\necho "Hello, $name"' > hello.sh
$  git restore hello.sh
$  git add hello.sh
```
*   Staging and Cleaning
```bash
$ printf '#!/bin/bash\n\n# This is an unwanted but staged comment\nname=${1:-"World"}\n\necho "Hello, $name"' > hello.sh
$ git restore --stage hello.sh
$ git restore hello.sh
```
*   Commiting and Reverting
```bash
$ printf '#!/bin/bash\n\n# This is an unwanted but committed change\nname=${1:-"World"}\n\necho "Hello, $name"' > hello.sh
$ git commit -m "chore: add unwanted committed change"
$ git revert HEAD
```
*   Tagging and Removing Commits
```bash
$ git tag oops
$ git reset --hard v1
```
*   Displaying Logs with Deleted Commits
```bash
$ git reflog
$ git show oops
```
*   Cleaning Unreferenced Commits
```bash 
$ git tag -d oops
$ git reflog expire --expire-unreachable=now --all
$ git gc --prune=now
```

*   Add Author Information 
```bash
$ printf '#!/bin/bash\n\n# Default is World\n# Author: Jim Weirich\nname=${1:-"World"}\n\necho "Hello, $name"' > hello.sh

$ printf '#!/bin/bash\n\n# Default is World\n# Author: Jim Weirich\n# Email: jim@gmail.com\nname=${1:-"World"}\n\necho "Hello, $name"' > hello.sh
$ git add hello.sh
$ git commit --amend --no-edit
```
## Move it
* move hello.sh into a lib/ then commit
```bash
$ mkdir -p lib
$ git mv hello.sh lib/
$ git commit -m "Move hello.sh to lib/ directory"
# create a makefile and commit
$ printf 'TARGET="lib/hello.sh"\n\nrun:\n\tbash ${TARGET}' > Makefile
$ git add Makefile
$ git commit -m "chore: add Makefile"
```

## blobs, trees, and commits
* Latest Object Hash
```bash
# find latest object hash
$ latest_object=$(find .git/objects -type f | xargs ls -lt | head -n 1 | awk -F/ '{print $(NF-1)$NF}')
$ $ echo "Latest object hash: $latest_object"
# print type
$ object_type=$(git cat-file -t $latest_object)
$ echo "Object type: $object_type"
# print content
$ echo "Object content:"
$ git cat-file -p $latest_object
# dump contents of hello.sh
$ cd lib
$ echo "Content of hello.sh:"
$ blob_hash=$(git ls-tree HEAD | grep 'hello.sh' | awk '{print $3}')
$ git cat-file -p $blob_hash
```
* Dumping Directory Tree
```bash
# dump directory tree using the hash
$ git ls-tree -r $latest_object
# dump lib/
$ echo "Contents of the lib/ directory:"
$ git ls-tree -r HEAD | grep 'lib/'
```
## Branching
```bash
# create and switch to a new branch
$ git checkout -b greet

# create new file in lib directory and add content then commit
$ cd lib
$ touch greeter.sh
$ printf '#!/bin/bash\n\nGreeter() {\n\twho="$1"\n\techo "Hello, $who"\n}' > greeter.sh
$ git add greeter.sh
$ git commit -m "chore: add greeter.sh"

# update the lib/hello.sh, stage and commit.
$ printf '#!/bin/bash\n\nsource lib/greeter.sh\n\nname="$1"\nif [ -z "$name" ]; then\n\tname="World"\nfi\n\nGreeter "$name"' > hello.sh
$ git add hello.sh
$ git commit -m "chore: update hello.sh file"

# update the Makefile and commit
$ printf '# Ensure it runs the updated lib/hello.sh file\nTARGET="lib/hello.sh"\n\nrun:\n\tbash ${TARGET}' > Makefile
$ git add Makefile
$ git commit -m "chore: update Makefile"

# switch back to the main branch
$ git checkout master
# compare Makefile
$ git diff master greet -- Makefile
# compare hello.sh
$ git diff master greet -- hello.sh
# compare greeter.sh
$ git diff master greet -- greeter.sh
# generate README
$ echo 'This is the Hello World example   from the git project.' > README.md
```
* Draw a commit tree diagram illustrating the diverging changes between all branches to demonstrate the branch history.
```bash
$ git log --oneline --graph --all --decorate
```
## Conflicts, merging and rebasing
* Merge Main into Greet Branch:
```bash
# merge into greet
$ git checkout greet
$ git merge master
# switch to main branch and update hello.sh
$ git checkout master
$ cd lib
$ printf '#!/bin/bash\n\necho "What'\''s your name"\nread my_name\n\necho "Hello, $my_name"' > hello.sh
$ git add hello.sh
$ git commit -m "chore: change hello.sh contents"
```
* Merging Main into Greet Branch (conflict)
```bash
# merge main branch into greet
$ git checkout greet
$ git merge master
```
Accept incoming from master then commit.
* Rebasing Greet Branch
```bash
# check for the hash of the point before initial merge
$ git log --oneline --graph --decorate --all
# do a hard reset to this point
$ git reset --hard 8378214
# rebase greet on top of main
$ git rebase master
```
* Merging Greet into Main
```bash
$ git checkout master
$ git merge greet
```
## Local and remote repositories
```bash
# make a clone of the repository hello
$ git clone hello cloned_hello
# show logs
$ cd cloned_hello
$ git log
# display name of the cloned repo and more info
$ git remote -v
# list all branches
$ git branch -a
# update README in the original repo and commit
$ cd ../hello
$ printf 'This is the Hello World example from the git pron the original)' > README.md
# inside cloned_hello fetch changes from remote and display logs
$ cd ../cloned_hello
$ git fetch
$ git log --oneline
# merge changes from remote main branch into local main branch
$ git checkout master
$ git merge origin/master
# add local branch greet tracking remote origin/greet
$ git checkout -b greet origin/greet
# add remote to git repo and push main and greet branches to remote
$ git remote add origin_rem https://learn.zone01kisumu.ke/git/
$ git push origin_rem master
$ git push origin_rem greet
```
## Bare repositories
```bash
# create bare repo named hello.git
$ cd ..
$ git clone --bare hello hello.git
# add hello.git as a remote to hello repo
$ cd hello
$ git remote add origin ../hello.git
# change readme in original repo, commit and push
$ printf 'This is the Hello World example from the git pron the original and pushed to shared)' > README.md
$ git add README.md
$ git commit -m "chore: change README and push to shared repo"
$ git push
# switch to cloned_hello and pull changes
$ cd cloned_hello
$ git remote add origin_shared ../hello.git
$ gut pull origin_shared master
```
