# Suggested git workflow
The example below shows the steps involved in working on a task and creating a pull requests based on the git principles defined in
[Coding standards](coding-standards.md "Coding standards").

Managing multiple PRs in parallel can be tricky. It is strongly recommended to keep tasks independent, each starting from the main branch.
If a task depends on work done in another branch then the task is blocked until the first PR is merged.

| Activity | Git commands | GitHub actions |
| -------- | ------------ | -------------- |
| **Start task 1 on a new branch** | `git checkout main`<br>`git checkout -b branch1` ||
| Commit changes | `git commit -am"Finished work item 1"` | |
| Check your local branch is up to date | `git checkout branch1`<br>`git fetch`<br>`git rebase origin/main` | |
| Create pull request PR#1 | `git push origin branch1` | GitHub will offer to help you **Compare and pull request.**<br>Explain your change and add a reviewer. |
| --- | --- | --- |
| **Start task 2 on a new branch** | `git checkout main`<br>`git pull`<br>`git checkout -b branch2` ||
| Commit changes | `git commit -am"Work in progress on task 2"` ||
| --- | --- | --- |
| **Reviewer suggests changes in PR#1**<br>Avoid rebasing once the PR is in review, even if there have been changes to main. | `git checkout branch1` ||
| Commit changes | `git commit -am"Updated task 1"` ||
| Update PR#1 |`git push` ||
| --- | --- | --- |
| **Continue with task 2** | `git checkout branch2` ||
| Commit changes, checking you are up to date with main as before | `git commit -am"Finished task 2"` ||
| Create PR#2 | `git push origin branch2` | GitHub will offer to help you **Compare and pull request.**<br>Explain your change and add a reviewer. |
| --- | --- | --- |
| **Reviewer approves PR#1** | | Squash and merge into main leaving one commit message. |
| Delete local branch1 | `git branch -d branch1` | If you have set "Automatically delete head branches" GitHub will have deleted the remote branch automatically. |
| --- | --- | --- |
| **Reviewer suggests changes in PR#2** | `git checkout branch2` ||
| Check your local branch is up to date including PR#1 | `git fetch`<br>`git rebase origin/main` | |
| Commit changes | `git commit -am"Updated task 2"` ||
| Update PR#2 |`git push` ||
| --- | --- | --- |
| **Reviewer approves PR#2** | | Squash and merge into main leaving one commit message. |
| Delete local branch2 | `git branch -d branch2` | If you have set "Automatically delete head branches" GitHub will have deleted the remote branch automatically. |
| --- | --- | --- |
| And so on... | `git checkout main`<br>`git pull` ||

## Other questions...
* Should we promote `git switch <branch>` and `git switch -c <branch>` instead of `git checkout <branch>` and `git checkout -b <branch>`?
