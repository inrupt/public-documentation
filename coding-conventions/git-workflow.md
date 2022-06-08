# Suggested git workflow
The example below shows the steps involved in working on a task and creating a pull request based on the git principles 
defined in our [Coding standards](coding-standards.md "Coding standards").

Managing multiple PRs in parallel can be tricky. It is strongly recommended to keep tasks independent, each starting 
from the `main` branch. If a task depends on work done in another branch then, generally speaking, we'd advise 
considering that task blocked until the other branch is merged.

| Activity | Git commands | GitHub actions |
| -------- | ------------ | -------------- |
| **Start task 1 on a new branch** | `git checkout main`<br>`git checkout -b branch1` ||
| Commit changes | `git commit -am"Finished work item 1"` | |
| Check your local branch is up to date with `main`| See [Rebasing a branch](#rebasing-a-branch) | |
| Create pull request PR#1 | `git push origin branch1` | GitHub will offer to help you **Compare and pull request.**<br>Explain your change and add a reviewer. |
| --- | --- | --- |
| **Start task 2 on a new branch** | `git checkout main`<br>`git pull` or `git fetch; git rebase`<br>`git checkout -b branch2` ||
| Commit changes | `git commit -am"Work in progress on task 2"` ||
| --- | --- | --- |
| **Reviewer suggests changes in PR#1**<br>(Avoid rebasing once the PR is in review, if you can) | `git checkout branch1` ||
| If necessary, update your local branch | See [Rebasing a branch](#rebasing-a-branch) | |
| Commit changes | `git commit -am"Updated task 1"` ||
| Update PR#1 |`git push origin branch1`<br>or, if you rebased, see [Updating a PR](#updating-a-pr)||
| --- | --- | --- |
| **Continue with task 2** | `git checkout branch2` ||
| Commit changes, checking you are up to date with `main` as before | `git commit -am"Finished task 2"` ||
| Create PR#2 | `git push origin branch2` | GitHub will offer to help you **Compare and pull request.**<br>Explain your change and add a reviewer. |
| --- | --- | --- |
| **Reviewer approves PR#1** | | Squash and merge into `main`, leaving one commit message. |
| Delete local branch1 | `git branch -d branch1` | If you have set "Automatically delete head branches" GitHub will have deleted the remote branch automatically. |
| --- | --- | --- |
| **Reviewer suggests changes in PR#2** | `git checkout branch2` ||
| If necessary, update your local branch to include the PR#1 commits that are now in `main` | See [Rebasing a branch](#rebasing-a-branch) | |
| Commit more changes | `git commit -am"Updated task 2"` ||
| Update PR#2 |`git push origin branch2`<br>or, if you rebased, see [Updating a PR](#updating-a-pr)||
| --- | --- | --- |
| **Reviewer approves PR#2** | | Squash and merge into `main`, leaving one commit message. |
| Delete local branch2 | `git branch -d branch2` | If you have set "Automatically delete head branches" GitHub will have deleted the remote branch automatically. |
| --- | --- | --- |
| And so on... | `git checkout main`<br>`git pull` ||

## Rebasing a branch
You often need to update a branch to include commits that were added to `main` after you created the branch.
The steps are along the lines of the following:
```shell
git checkout <branch>
git fetch
git rebase origin/main
```

This takes the commits in your branch and replays them on top of the newer commits from `main`.
At this point you may have some merge conflicts. Once these have been addressed you need to add them to the commit and
continue the rebase.
```shell
git add <conflicted_files>
git rebase --continue
```

## Updating a PR
If you have made changes to a local branch that is tracking a remote branch (PR) there are 2 ways to update it. 
Firstly, if you have not rebased the branch you can update this with:
```shell
git push origin <branch>
# or, if upstream is set this can be simplified to
git push
```

However, if you have rebased, then your commits will have different ids to the commits in the remote branch even 
though they look the same. This would make the `push` command above fail like this:
```shell
error: failed to push some refs to 'github.com:<repo>.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
If no-one else is working on that branch and you are about to merge it into `main` in the remote repository,
then you can force the push. The force option used below is a safer option that just `--force` as it will fail if
someone else has commited to the remote branch.
```shell
git push origin --force-with-lease <branch>
```

## Other questions...
* Should we promote `git switch <branch>` and `git switch -c <branch>` instead of `git checkout <branch>` and
  `git checkout -b <branch>`?
