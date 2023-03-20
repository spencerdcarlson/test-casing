# test-casing
Test git case sensitivity

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

