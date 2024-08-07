
cheet sheet
```bash
git stash save "Work in progress"
git stash pop
git diff 
git
#last 2 commits diff
git diff HEAD~2 HEAD
```


# combine last n commits to one commit (squash) 
```bass
# removes n comints
git reset --hard HEAD~3
# changes are pushed to staing
git merge --squash HEAD@{1}
# commont with latest code
git commit -m ""
# force push
git push -f
```
# Revert  changes in local
```bash
# change commit id
git commit --amend -m "sdf"

# what ever changes are made to staging add to the last commit
git commit --amend

# in local revert all change
git reset --hard HEAD(select the starting commit id to whcih you need)  

# changes to staging
git reset --soft HEAD~1

# changes to loval
git reset  HEAD~1

# in remote 
git revert <commit to revert>

# staging to local specific file 
git reset HEAD filepath

# revert all changes in local
git chekout filepath

# clean all local
git clean -df 

# to chekwhat have done locally
git reflog 
```
#  cheerypick from other branch
```bash
git checkout feature
git log  --> commit id abc123

git checkout main
git cherry-pick abc123

# reslove confilts
git add <resolved-files>


# continue
git cherry-pick --continue

# abaort
git cherry-pick --abort
```

# commit to new branch
```bash
git push origin develop:update-docker-imag
```
