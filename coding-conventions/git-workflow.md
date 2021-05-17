# Suggested git workflow
The example below shows how you could manage multiple tasks and pull requests in parallel based on the git principles defined in
[Coding standards](coding-standards.md "Coding standards").


| Activity | Git commands | GitHub actions |
| -------- | ------------ | -------------- |
| **Start task 1 on a new branch** | `git checkout main`<br>`git checkout -b branch1` ||
| Commit changes | `git commit -am"Finished work item 1"` | |
| Check your local branch is up to date | `git checkout main`<br>`git pull`<br>`git checkout branch1`<br>`git rebase main` | |
| Create pull request PR#1 | `git push origin branch1` | GitHub will offer to help you **Compare and pull request**<br>Explain your change and add a reviewer |
| --- | --- | --- |
| **Start task 2 on a new branch** | `git checkout main`<br>`git pull`<br>`git checkout -b branch2` ||
| **Q:** What if you need branch1 code in your work? | Would you merge/rebase that branch in or branch from the branch?|
| Commit changes | `git commit -am"Work in progress on task 2"` ||
| --- | --- | --- |
| **Reviewer suggests changes in PR#1** | `git checkout branch1` ||
| **Q:** What if there are changes on master to incorporate? Do you do another rebase but this is now a public branch so how do you then push? || 
| Commit changes | `git commit -am"Updated task 1"` ||
| Update PR#1 |`git push` ||
| --- | --- | --- |
| **Continue with task 2** | `git checkout branch2` ||
| **Q:** How to you update this to include the branch1 changes? ||
| Commit changes, checking you are up to date with main as before | `git commit -am"Finished task 2"` ||
| Create PR#2 | `git push origin branch2` | GitHub will offer to help you **Compare and pull request**<br>Explain your change and add a reviewer |
| --- | --- | --- |
| **Reviewer approves PR#1** | | Squash and merge into main leaving one commit message |
| Delete branch1 | `git branch -d branch1`<br>`git push origin --delete branch1` | You can also delete the remote branch in github instead of using the 2nd command |
| **Q:** Do we rebase local branch2 on main? ||
| --- | --- | --- |
| **Reviewer suggests changes in PR#2** | `git checkout branch2` ||
| Commit changes | `git commit -am"Updated task 2"` ||
| Update PR#2 |`git push` ||
| --- | --- | --- |
| **Reviewer approves PR#2** | | Squash and merge into main leaving one commit message<br>**Q:** Is there a rebase at this point in GitHub? |
| Delete branch2 | `git branch -d branch2`<br>`git push origin --delete branch2` | You can also delete the remote branch in github instead of using the 2nd command |
| --- | --- | --- |
| And so on... | `git checkout main`<br>`git pull` ||

## Other thoughts
* Should we promote `git switch <branch>` and `git switch -c <branch>` instead of `git checkout <branch>` and `git checkout -b <branch>`?
