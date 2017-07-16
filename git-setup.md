

Additional Notes:

* Maintaining a fork:

1. Fork and clone repo:

`git clone git@github.com:YOUR-USERNAME/YOUR-FORKED-REPO.git`

2. Add remote from the original repository in your forked repository:

```
cd into/cloned/fork-repo
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git
git fetch upstream
```

3. Update your fork to keep up with their changes:

`git pull upstream master`
