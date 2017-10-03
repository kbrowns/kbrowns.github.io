# git cheats
## squashing commits and keeping commit history
When you want to keep the commit history from your branch once merged to the other branch.

Refresh your local master
```
git checkout master
git pull
```
For safety, use a temp branch of your branch
```
git checkout myfeature
git checkout -b temp
```
Do the squash
```
git rebase -i master
```
this will launch your editor (mine is configured to use sublime). For all commits after the first flip "pick" to "squash".
Merge your temp branch to master and push it
```
git checkout master
git merge --ff temp
git push
```

## squashing commits and discarding commit history
if you don't care about commit history, or in some cases care to get rid of that history
(often the feature branch has a lot of developer workflow goo that don't make sense historically),
then do this:

Get on a fresh master:
```
git checkout master
git pull --rebase
```
Do the squash
```
git merge --squash branch/name
git commit
```
