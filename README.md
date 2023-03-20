# test-casing
Test git case sensitivity issue.
This is to demonstrate how `git` will continuisly calim that some branches are always _new_ despite the local repository actually containing an in sync ref 
when there is a case mismatch issue.

## System Info
```
[System Info]
git version:
git version 2.37.1 (Apple Git-137.1)
cpu: x86_64
no commit associated with this build
sizeof-long: 8
sizeof-size_t: 8
shell-path: /bin/sh
feature: fsmonitor--daemon
uname: Darwin 22.3.0 Darwin Kernel Version 22.3.0: Mon Jan 30 20:42:11 PST 2023; root:xnu-8792.81.3~2/RELEASE_X86_64 x86_64
compiler info: clang: 14.0.0 (clang-1400.0.29.202)
libc info: no libc information available
$SHELL (typically, interactive shell): /usr/local/Cellar/bash/5.2.15/bin/bash
```

## Recreate The Problem 
This example creates two branches `Bug/foo` and `bug/bar` in a remote repository and demonstrates how `git` will always output that `bug/bar` is a new branch when it is not.

```bash
# Create a new repository
mkdir test-casing
cd test-casing/
git init
echo "# test-casing" >> README.md
git add -A
git commit -am "init commit"
git branch -M master
git remote add origin git@github.com:spencerdcarlson/test-casing.git
git push -u origin master

# Create a new "Bug" branch
git checkout -b Bug/foo
touch foo
git add -A
git commit -am "added foo"
git push --set-upstream origin Bug/foo

# At this point if I attempt to create a branch "bug/bar" with `git checkout -b bug/bar` 
# it is unable to create the local ref. I get this error when attempting to push
# "fatal: bug/bar cannot be resolved to branch"
# clone into another repo to create a conflict

# Clone repo into new folder and create a new "bug" branch
git clone git@github.com:spencerdcarlson/test-casing.git test-casing-2
cd test-casing-2/
git checkout -b bug/bar
touch bar
git add -A
git commit -am "added bar"
git push --set-upstream origin bug/bar

# Pull changes in origional repo
cd ../test-casing
git pull
# * [new branch]      bug/bar    -> origin/bug/bar
cat .git/refs/remotes/origin/Bug/bar
# 0a799176c557559ac435c7ed6af493927b654509
git pull
# * [new branch]      bug/bar    -> origin/bug/bar

# Regardless of how many times that `git pull` is executed
# the output claims that the branch "bug/bar" is new
# because on my local machine the ref is "Bug/bar" which is not 
# an exact match with "bug/bar".
# My local ref is able to sync with the remote repository.
# so there is no issue there, but this is problamatic when 
# there are hundreds of case mismatching 
# error messages in a repository.

