# git cheats
## squashing commits - approach 1
When working on a feature branch it's often advantageous to take a list of many commits from that branch and morph them into one big commit that goes into master. For our shop this is useful because commits to master are synched to SVN trunk and when there's many commits each one fires our CI infrastructure. So, many commits often creates a great deal of build noise. Squashing them reduces the noise, but also allows you to assign a new BugzID to that commit so that you avoid the commit being rejected due to an old BugzID. Here's how I do it:

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
