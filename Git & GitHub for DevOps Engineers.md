**Git \& GitHub for DevOps Engineers**



**1. Initial Setup**

```bash

git config --global user.name "Kashif Khan"

git config --global user.email "your@email.com"

git config --global init.defaultBranch main

git config --global core.editor "vim"

git config --list

```



**2. Repo Init \& Clone**

&#x20;```bash

git init

git clone <repo-url>

git clone -b <branch> <repo-url> .          # clone specific branch into current dir (common in CI)

git clone --depth 1 <repo-url>              # shallow clone — fast, used in CI/CD pipelines

```



**3. Daily Workflow**

```bash

git status

git add .

git commit -m "feat: add Jenkinsfile for staging deploy"

git push -u origin main

git push

git pull --rebase                           # preferred in DevOps to keep linear history

```



**4. Branching Strategy (GitFlow / Trunk-based)**

```bash

git branch -M main

git checkout -b feature/ci-pipeline

git checkout -b release/v1.2.0

git checkout -b hotfix/prod-nginx-fix

git switch -c develop

git branch -a                               # list local + remote branches

git push origin --delete feature/ci-pipeline

```



**5. Remote Management (multi-environment repos)**

```bash

git remote add origin <repo-url>

git remote add upstream <repo-url>          # for forked infra repos

git remote -v

git fetch --all

git remote set-url origin <new-url>

```



**6. Tags \& Releases (critical for deployment tracking)**

```bash

git tag -a v1.0.0 -m "Production release v1.0.0"

git push origin v1.0.0

git push origin --tags

git tag -l "v1.\*"                           # list version tags (used in rollback scripts)

git checkout tags/v1.0.0                    # checkout exact release for deployment

```



**7. Undo / Recovery (incident response scenarios)**

```bash

git restore <file>

git restore --staged <file>

git reset --soft HEAD\~1                     # undo commit, keep staged (fix commit message)

git reset --mixed HEAD\~1                    # undo commit, unstage changes (default)

git reset --hard HEAD\~1                     # nuke last commit + changes (rollback)

git revert <commit-hash>                    # safe undo on shared/prod branches (creates new commit)

git reflog                                  # recover "lost" commits after bad reset

```



**8. Merge / Rebase (clean history for CI logs)**

```bash

git merge --no-ff feature/ci-pipeline       # explicit merge commit (preserves audit trail)

git rebase main                             # update feature branch with latest main

git rebase -i HEAD\~5                        # squash commits before merging to main

git merge --abort

git rebase --abort

```



**9. Stash (quick context switch during incidents)**

```bash

git stash

git stash push -m "WIP: nginx config debug"

git stash list

git stash pop

git stash apply stash@{1}

git stash drop

```



**10. Hooks (automation — relevant to CI/CD)**

```bash

\# Located in .git/hooks/ — rename by removing .sample

.git/hooks/pre-commit                       # run lint/tests before commit

.git/hooks/pre-push                         # run tests before push

.git/hooks/post-merge                       # auto-run after pull/merge (e.g., reinstall deps)



\# Example: make a hook executable

chmod +x .git/hooks/pre-commit

```



**11. Submodules (common in infra-as-code repos)**

```bash

git submodule add <repo-url> modules/network

git submodule update --init --recursive

git submodule foreach git pull origin main

```



**12. CI/CD-Specific Commands**

```bash

git log -1 --pretty=%H                      # get latest commit hash (used in pipeline scripts)

git rev-parse HEAD                          # same purpose, common in Jenkinsfiles

git diff --name-only HEAD\~1 HEAD            # list changed files (for selective deploy)

git log --since="1 day ago" --oneline       # changelog generation

git describe --tags                         # get current version label for build artifacts

```



**13. Cherry-pick (hotfix deployment to multiple branches)**

```bash

git cherry-pick <commit-hash>

git cherry-pick <hash1> <hash2>

git cherry-pick --abort

```



**14. Cleanup**

```bash

git clean -fd                               # remove untracked files

git gc                                      # garbage collect, optimize repo size

git fsck                                    # check repo integrity

git remote prune origin                     # remove stale remote-tracking branches

```



**15. Aliases (speed up your terminal workflow)**

```bash

git config --global alias.st status

git config --global alias.co checkout

git config --global alias.br branch

git config --global alias.cm "commit -m"

git config --global alias.last "log -1 HEAD"

git config --global alias.unstage "reset HEAD --"

```



**Quick Reference: Branching Model for Infra Repos**

```

main        → production-ready, protected branch

develop     → integration branch

feature/\*   → new features (Jenkins pipelines, Terraform modules, etc.)

release/\*   → pre-prod staging

hotfix/\*    → urgent prod fixes, merged into main + develop

```



Two corrections to keep in mind from before: `--soft` keeps changes staged, `--mixed` (default) unstages but keeps them in your working directory — useful to remember when you're rolling back a bad config commit mid-incident.

