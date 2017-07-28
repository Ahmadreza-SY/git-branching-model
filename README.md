# A Git branching model
# ALL IN ONE :
![Image of Yaktocat](http://nvie.com/img/git-model@2x.png) </br>

# Main Branches : develop and master;
When the source code in the develop branch reaches a stable point and is ready to be released, all of the changes should be merged back into master somehow and then tagged with a release number.
# Supporting branches :

## Feature branches
A future feature that someone wants to develop. </br> 
+ May branch off from: </br>
    develop </br>
+ Must merge back into: </br>
    develop </br>
+ Branch naming convention: </br>
    anything except master, develop, release-*, or hotfix-* </br>

+ When starting work on a new feature, branch off from the develop branch.

```bash
$ git checkout -b myfeature develop
Switched to a new branch "myfeature"
```

+ Finished features may be merged into the develop branch to definitely add them to the upcoming release:

```bash
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff myfeature
Updating ea1b82a..05e9557
(Summary of changes)
$ git branch -d myfeature
Deleted branch myfeature (was 05e9557).
$ git push origin develop
```
The --no-ff flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature.

## Release branches
Preparation of a new production release, minor bug fixes, meta-data for a release (version number, build dates, etc.). </br>

+ May branch off from: </br>
     develop </br>
+ Must merge back into: </br>
     develop and master </br>
+ Branch naming convention: </br>
     release-* </br>
     
+ Creating a release branch
```shell
$ git checkout -b release-1.2 develop
Switched to a new branch "release-1.2"
$ ./bump-version.sh 1.2
Files modified successfully, version bumped to 1.2.
$ git commit -a -m "Bumped version number to 1.2"
[release-1.2 74d9424] Bumped version number to 1.2
1 files changed, 1 insertions(+), 1 deletions(-)
```
+ Finishing a release branch

The first two steps in Git:
```shell
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2
```
To keep the changes made in the release branch, we need to merge those back into develop, though. In Git:
```shell
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
```
This step may well lead to a merge conflict (probably even, since we have changed the version number). If so, fix it and commit.
Now we are really done and the release branch may be removed, since we don’t need it anymore:
```shell
$ git branch -d release-1.2
Deleted branch release-1.2 (was ff452fe).
```

## Hotfix Branches

When a critical bug in a production version must be resolved immediately, a hotfix branch may be branched off from the corresponding tag on the master branch that marks the production version. </br>

+ May branch off from: </br>
    master </br>
+ Must merge back into: </br>
    develop and master </br>
+ Branch naming convention: </br>
    hotfix-* </br>

+ Creating the hotfix branch
```shell
$ git checkout -b hotfix-1.2.1 master
Switched to a new branch "hotfix-1.2.1"
$ ./bump-version.sh 1.2.1
Files modified successfully, version bumped to 1.2.1.
$ git commit -a -m "Bumped version number to 1.2.1"
[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
1 files changed, 1 insertions(+), 1 deletions(-)
```
Don’t forget to bump the version number after branching off!

Then, fix the bug and commit the fix in one or more separate commits.
```shell
$ git commit -m "Fixed severe production problem"
[hotfix-1.2.1 abbe5d6] Fixed severe production problem
5 files changed, 32 insertions(+), 17 deletions(-)
```
+ Finishing a hotfix branch
First, update master and tag the release.
```shell
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2.1
```
Next, include the bugfix in develop, too:
```shell
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
```
Finally, remove the temporary branch:
```shell
$ git branch -d hotfix-1.2.1
Deleted branch hotfix-1.2.1 (was abbe5d6).
```
