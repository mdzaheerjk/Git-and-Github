# Git — Complete Reference Notes

> Every Git concept and command you need: setup, branching, merging, rebasing, undoing, collaboration, advanced workflows, and more.

---

## Setup & Configuration

### Identity (required before first commit)

```bash
git config --global user.name  "Alice Smith"
git config --global user.email "alice@example.com"
```

### Configuration levels

```bash
# Three levels — each overrides the one above
git config --system   # /etc/gitconfig        — all users on machine
git config --global   # ~/.gitconfig           — current user
git config --local    # .git/config            — current repo (default)
```

### Common global settings

```bash
git config --global core.editor       "code --wait"     # VS Code
git config --global core.editor       "vim"
git config --global core.autocrlf     input             # Linux/Mac
git config --global core.autocrlf     true              # Windows
git config --global core.ignorecase   false
git config --global init.defaultBranch main
git config --global push.default      current           # push to same-name remote branch
git config --global pull.rebase       false             # pull = merge (default)
git config --global pull.rebase       true              # pull = rebase
git config --global merge.tool        vimdiff
git config --global diff.tool         vscode
git config --global color.ui          auto
git config --global core.pager        less
git config --global credential.helper cache             # cache password
git config --global credential.helper store             # store password on disk
git config --global alias.st          status
git config --global alias.co          checkout
git config --global alias.lg         "log --oneline --graph --decorate --all"
```

### View & edit config

```bash
git config --list                          # all settings
git config --list --show-origin            # with file locations
git config user.name                       # read one value
git config --global --edit                 # open global config in editor
git config --global --unset user.email     # remove a setting
cat ~/.gitconfig                           # view raw file
```

---

## Initialise & Clone

```bash
# New repository
git init                            # init in current directory
git init my-project                 # init in new directory
git init --bare repo.git            # bare repo (no working tree — for servers)

# Clone
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder   # into custom folder
git clone git@github.com:user/repo.git                 # SSH
git clone --depth 1 https://github.com/user/repo.git   # shallow clone (latest only)
git clone --depth 1 --branch main https://...          # shallow specific branch
git clone --single-branch --branch dev https://...     # clone one branch only
git clone --bare https://github.com/user/repo.git      # bare clone
git clone --mirror https://github.com/user/repo.git    # full mirror (for backup)
git clone --recurse-submodules https://...             # include submodules
```

---

## The Three Areas

```
Working Directory  →  Staging Area (Index)  →  Repository (.git)
     edit                  git add                git commit
     ←──── git restore ────┤                     │
     ←──────────────── git restore HEAD ─────────┘
```

---

## Status & Inspection

```bash
git status                          # full status
git status -s                       # short format
git status -sb                      # short + branch info

# Short format symbols:
# ?? = untracked   M = modified   A = added   D = deleted
# R = renamed      C = copied     U = updated but unmerged
# First column = staging area, second = working directory
```

---

## Staging (git add)

```bash
git add file.txt                    # stage one file
git add src/                        # stage entire directory
git add .                           # stage everything in current dir
git add -A                          # stage all: new + modified + deleted
git add -u                          # stage modified + deleted (not new files)
git add *.py                        # glob pattern
git add -p                          # interactive patch — choose hunks to stage
git add -p file.txt                 # patch mode on one file
git add -i                          # interactive mode (menu)
git add -N file.txt                 # mark as "intent to add" (shows in diff)
```

### Unstage

```bash
git restore --staged file.txt       # unstage (keep working dir changes)
git restore --staged .              # unstage everything
git reset HEAD file.txt             # older equivalent
git reset HEAD                      # unstage all
```

---

## Committing

```bash
git commit                          # open editor for message
git commit -m "feat: add login page"
git commit -am "fix: typo"          # stage tracked files + commit (skips git add)
git commit --allow-empty -m "chore: trigger CI"
git commit -v                       # show diff in editor when writing message
git commit --no-verify              # skip pre-commit hooks

# Amend the most recent commit (only before pushing)
git commit --amend                  # edit message + include staged changes
git commit --amend -m "new message" # just change message
git commit --amend --no-edit        # add staged changes, keep message
git commit --amend --reset-author   # update author info
```

### Conventional Commits format

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`, `revert`

```bash
git commit -m "feat(auth): add JWT token refresh"
git commit -m "fix(api): handle null response from payment gateway"
git commit -m "docs: update README with setup instructions"
```

---

## Viewing History

```bash
git log                             # full log
git log --oneline                   # one line per commit
git log --oneline --graph           # ASCII branch graph
git log --oneline --graph --all     # all branches in graph
git log --oneline --graph --decorate --all   # with branch/tag labels
git log -n 5                        # last 5 commits
git log -5                          # same
git log --since="2024-01-01"
git log --until="2024-12-31"
git log --since="2 weeks ago"
git log --after="yesterday"
git log --author="Alice"
git log --author="Alice\|Bob"       # multiple authors
git log --grep="login"              # search commit messages
git log --grep="fix" --all-match    # multiple greps (AND)
git log -S "function login"         # pickaxe — commits that added/removed string
git log -G "regex pattern"          # pickaxe with regex
git log --diff-filter=A             # only commits that Added files
git log --diff-filter=D             # only commits that Deleted files
git log -- path/to/file             # commits touching this file
git log --follow -- path/to/file    # follow renames
git log --stat                      # show file change stats
git log --patch                     # show full diffs
git log --patch -p file.txt         # diffs for one file
git log --pretty=format:"%h %an %ar %s"   # custom format
git log --pretty=oneline
git log --simplify-by-decoration    # only commits with tags/branches
git log main..feature               # commits in feature not in main
git log main...feature              # commits in either, not both (symmetric diff)
git log HEAD~5..HEAD                # last 5 commits
git shortlog                        # grouped by author
git shortlog -sn                    # commit count per author, sorted
```

### Single commit inspection

```bash
git show abc1234                    # show commit
git show HEAD                       # show latest commit
git show HEAD~2                     # 2 commits back
git show HEAD:file.txt              # file contents at HEAD
git show v1.0:src/app.py            # file at a tag
git show --stat abc1234             # just the stats
git show --name-only abc1234        # just the filenames changed
```

---

## Diffing

```bash
git diff                            # working dir vs staging area (unstaged changes)
git diff --staged                   # staging area vs last commit (what will commit)
git diff HEAD                       # working dir vs last commit (all changes)
git diff abc1234                    # working dir vs that commit
git diff abc1234 def5678            # between two commits
git diff main..feature              # between two branches (tips)
git diff main...feature             # since they diverged (common ancestor)
git diff HEAD~3 HEAD                # last 3 commits
git diff HEAD~3 HEAD -- file.txt    # for one file
git diff --stat                     # summary only
git diff --name-only                # just filenames
git diff --name-status              # filenames + A/M/D status
git diff --word-diff                # word-level diff
git diff --color-words              # coloured word diff
git diff -w                         # ignore whitespace
git diff -b                         # ignore whitespace changes
git diff --ignore-blank-lines
git difftool                        # open in configured diff tool
```

---

## .gitignore

```bash
# File patterns
*.log              # all .log files anywhere
*.py[cod]          # .pyc, .pyo, .pyd
!important.log     # negate: do NOT ignore this file
build/             # ignore directory
/build             # only ignore at root level
doc/*.txt          # ignore txt only in doc/ (not doc/sub/)
doc/**/*.txt       # ignore txt in doc/ and all subdirs
**/logs            # logs/ in any subdirectory
logs/**            # everything inside logs/

# Check what's being ignored
git check-ignore -v file.txt        # why is this file ignored?
git status --ignored                # show ignored files
git ls-files --others --ignored --exclude-standard

# Force add ignored file
git add -f ignored-file.txt

# Stop tracking a file that's now in .gitignore
git rm --cached file.txt            # remove from index only (keep in working dir)
git rm --cached -r build/           # recursively

# Global gitignore (for editor files, OS files)
git config --global core.excludesFile ~/.gitignore_global
# ~/.gitignore_global:
.DS_Store
Thumbs.db
.idea/
.vscode/
*.swp
```

---

## Branches

### Create & switch

```bash
git branch                          # list local branches
git branch -a                       # list all (local + remote)
git branch -v                       # with last commit info
git branch -vv                      # with upstream tracking info
git branch --merged                 # branches merged into current
git branch --no-merged              # branches not yet merged

git branch feature                  # create branch (stay on current)
git switch feature                  # switch to branch
git switch -c feature               # create + switch (modern)
git checkout -b feature             # create + switch (classic)
git checkout -b feature main        # create from main
git switch -c feature origin/feature  # track remote branch

git switch -                        # switch to previous branch
git checkout -                      # same
```

### Rename & delete

```bash
git branch -m old-name new-name     # rename local branch
git branch -m new-name              # rename current branch

git branch -d feature               # delete (safe — must be merged)
git branch -D feature               # force delete (even if unmerged)
git push origin --delete feature    # delete remote branch
git push origin :feature            # same (older syntax)
```

### Tracking remote branches

```bash
git branch --set-upstream-to=origin/main main    # set upstream
git branch -u origin/main                        # shorthand
git branch --unset-upstream                      # remove upstream
```

---

## Merging

```bash
git merge feature                   # merge feature into current branch
git merge --no-ff feature           # always create merge commit (no fast-forward)
git merge --ff-only feature         # only fast-forward, fail otherwise
git merge --squash feature          # squash all commits into one staged change
git merge --abort                   # cancel in-progress merge
git merge --continue                # continue after resolving conflicts

# Merge with commit message
git merge -m "merge feature/login into main" feature

# Merge specific commit
git cherry-pick abc1234             # apply single commit to current branch
git cherry-pick abc1234 def5678     # apply multiple commits
git cherry-pick abc1234..def5678    # apply a range (excludes abc1234)
git cherry-pick abc1234^..def5678   # apply a range (includes abc1234)
git cherry-pick --no-commit abc1234 # stage changes without committing
git cherry-pick --abort
git cherry-pick --continue
```

### Fast-forward vs Merge commit

```
Fast-forward (linear):             Merge commit (preserves history):
  A - B - C (main)                   A - B - C (main)
           \                                   \
            D - E (feature)          D - E ---- M  ← merge commit
      becomes:                                (main)
  A - B - C - D - E (main)
```

### Resolving conflicts

```bash
# When conflict occurs, files contain markers:
<<<<<<< HEAD
  current branch content
=======
  incoming branch content
>>>>>>> feature

# Options:
git status                          # see which files conflict
git diff                            # see all conflicts
git mergetool                       # open visual merge tool

# Accept one side entirely
git checkout --ours   file.txt      # keep current branch version
git checkout --theirs file.txt      # take incoming version
git restore --ours    file.txt      # modern syntax
git restore --theirs  file.txt

# After resolving manually:
git add file.txt                    # mark as resolved
git commit                          # complete the merge
git merge --abort                   # start over / cancel
```

---

## Rebasing

Rebase rewrites commits to apply on top of another branch — produces a linear history.

```bash
git rebase main                     # rebase current branch onto main
git rebase main feature             # rebase feature onto main (from any branch)
git rebase --onto main server client  # rebase client onto main (excluding server)
git rebase --abort                  # cancel rebase
git rebase --continue               # after resolving conflict
git rebase --skip                   # skip current conflicting commit

# Pull with rebase
git pull --rebase
git pull --rebase=interactive
```

### Interactive Rebase (rewriting history)

```bash
git rebase -i HEAD~4                # interactive rebase of last 4 commits
git rebase -i abc1234               # rebase from this commit (exclusive)
git rebase -i --root                # rebase all commits from root
```

Commands in the interactive editor:

```
pick   abc1 commit message          # keep commit as-is
reword abc2 commit message          # keep commit, edit message
edit   abc3 commit message          # pause to amend this commit
squash abc4 commit message          # meld into previous commit (keep message)
fixup  abc5 commit message          # meld into previous, discard this message
fixup -C abc5 message               # use this commit's message instead
drop   abc6 commit message          # remove commit entirely
exec   make test                    # run shell command between commits
break                               # pause here
label  my-label                     # label this point
reset  my-label                     # reset back to label
merge  my-label                     # create merge commit
```

```bash
# After `edit` command: make changes, then:
git add .
git commit --amend
git rebase --continue

# Squash last 3 commits into one (alternative)
git reset --soft HEAD~3             # undo commits, keep staged
git commit -m "combined commit"
```

### Rebase vs Merge

| | Rebase | Merge |
|--|--------|-------|
| History | Linear, clean | Preserves branch structure |
| Commits | Rewritten (new SHAs) | Original SHAs preserved |
| Use when | Local feature branches | Public/shared branches |
| Golden rule | **Never rebase commits already pushed to shared branch** | Safe to use any time |

---

## Remote Repositories

```bash
git remote                          # list remotes
git remote -v                       # with URLs
git remote show origin              # detailed info about remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git  # for forks
git remote rename origin old-origin
git remote remove origin
git remote set-url origin https://github.com/user/new-repo.git
git remote set-url --push origin https://different-push-url.git
git remote prune origin             # remove stale remote-tracking branches
```

### Fetch, Pull, Push

```bash
# Fetch — download changes, don't integrate
git fetch origin                    # fetch all branches from origin
git fetch origin main               # fetch specific branch
git fetch --all                     # fetch all remotes
git fetch --prune                   # fetch + remove stale remote branches
git fetch --tags                    # fetch all tags

# Pull — fetch + merge (or rebase)
git pull                            # fetch + merge current branch
git pull origin main                # fetch + merge specific branch
git pull --rebase                   # fetch + rebase instead of merge
git pull --ff-only                  # fail if not fast-forwardable
git pull --no-commit                # merge but don't auto-commit

# Push — send commits to remote
git push                            # push current branch to its upstream
git push origin main                # push specific branch
git push -u origin feature          # push + set upstream tracking (-u = --set-upstream)
git push --all origin               # push all local branches
git push --tags                     # push all tags
git push --force                    # force push — DANGEROUS on shared branches
git push --force-with-lease         # safer force: fails if remote has new commits
git push origin --delete feature    # delete remote branch
git push origin :feature            # same (old syntax)
git push origin HEAD                # push current branch by name
```

### Working with forks

```bash
git remote add upstream https://github.com/original/repo.git
git fetch upstream
git merge upstream/main             # bring in upstream changes
git rebase upstream/main            # rebase onto upstream
git push origin main                # push to your fork
```

---

## Undoing Changes

### Working directory (unstaged)

```bash
git restore file.txt                # discard changes (irreversible)
git restore .                       # discard all changes
git restore --source=HEAD~2 file.txt  # restore file to 2 commits ago
git checkout -- file.txt            # older equivalent of restore
git clean -f                        # remove untracked files
git clean -fd                       # remove untracked files and directories
git clean -fX                       # remove ignored files only
git clean -fx                       # remove ignored + untracked
git clean -n                        # dry run (show what would be deleted)
git clean -nd                       # dry run including dirs
```

### Staging area

```bash
git restore --staged file.txt       # unstage file (keep working dir)
git restore --staged .              # unstage everything
git reset HEAD file.txt             # older equivalent
```

### Commits — git reset

```bash
# --soft: undo commit, keep changes staged
git reset --soft HEAD~1             # undo last commit, keep staged
git reset --soft HEAD~3             # undo last 3 commits, keep staged

# --mixed (default): undo commit, keep changes in working dir (unstaged)
git reset HEAD~1                    # undo last commit, unstage changes
git reset --mixed HEAD~1            # same

# --hard: undo commit AND discard changes (DESTRUCTIVE)
git reset --hard HEAD~1             # undo last commit + delete changes
git reset --hard HEAD               # discard all uncommitted changes
git reset --hard origin/main        # reset to match remote main

# Reset to specific commit
git reset --soft abc1234
git reset --hard abc1234
```

### Commits — git revert (safe for public branches)

```bash
git revert HEAD                     # create new commit that undoes HEAD
git revert abc1234                  # revert a specific commit
git revert HEAD~3..HEAD             # revert a range (newest first)
git revert --no-commit HEAD~3..HEAD # stage all reverts, then commit once
git revert --abort
git revert --continue
git revert -m 1 abc1234             # revert a merge commit (keep parent 1)
```

### reset vs revert

| | reset | revert |
|--|-------|--------|
| What it does | Moves branch pointer back | Creates new undo commit |
| History | Rewrites (commits gone) | Preserved (safe) |
| Use for | Local unpushed commits | Pushed / shared commits |

### Recovering lost commits (reflog)

```bash
git reflog                          # history of HEAD movements
git reflog show main                # reflog for a branch
git reflog --all                    # all refs
git reset --hard HEAD@{3}           # go back to 3 moves ago
git checkout abc1234                # check out a lost commit
git branch recovered abc1234        # create branch at lost commit
```

---

## Stashing

Stash saves uncommitted work temporarily.

```bash
git stash                           # stash tracked changes (working dir + staged)
git stash push                      # same (explicit)
git stash push -m "work in progress on login"  # with description
git stash push --include-untracked  # also stash untracked files (-u)
git stash push --all                # also stash ignored files (-a)
git stash push -- file.txt          # stash only one file
git stash -p                        # interactive: choose hunks to stash

git stash list                      # show all stashes
git stash show                      # show most recent stash diff (stats)
git stash show -p                   # full diff
git stash show stash@{2}            # show specific stash

git stash pop                       # apply most recent + remove from stash
git stash apply                     # apply most recent, keep in stash
git stash apply stash@{2}           # apply specific stash
git stash pop stash@{1}

git stash drop                      # delete most recent stash
git stash drop stash@{2}            # delete specific stash
git stash clear                     # delete ALL stashes

git stash branch feature stash@{1}  # create new branch from stash + pop
```

---

## Tags

Tags mark specific commits — usually releases.

```bash
# Lightweight tag (just a pointer)
git tag v1.0                        # tag current HEAD
git tag v1.0 abc1234                # tag a specific commit

# Annotated tag (full object with message, author, date — recommended)
git tag -a v1.0 -m "Version 1.0 release"
git tag -a v1.0 abc1234 -m "Tagging commit"
git tag -s v1.0 -m "Signed tag"    # GPG-signed tag

# List tags
git tag                             # all tags
git tag -l "v1.*"                   # filtered
git tag --sort=-version:refname     # sorted by semver descending
git tag -n                          # with tag messages

# Inspect
git show v1.0                       # show tag details + commit

# Push tags
git push origin v1.0                # push one tag
git push origin --tags              # push all tags
git push origin --follow-tags       # push only annotated tags

# Delete
git tag -d v1.0                     # delete local tag
git push origin --delete v1.0       # delete remote tag
git push origin :refs/tags/v1.0     # same

# Checkout tag (detached HEAD — make a branch if you want to work from it)
git checkout v1.0
git checkout -b hotfix v1.0         # branch from a tag

# Rename tag
git tag new-name old-name           # create new tag at same commit
git tag -d old-name                 # delete old tag
git push origin new-name :old-name  # push new, delete old on remote
```

---

## Advanced Operations

### git bisect (binary search for bug)

```bash
git bisect start
git bisect bad                      # current commit is bad
git bisect good v1.0                # last known good commit

# Git checks out a midpoint commit — test it, then:
git bisect good                     # this commit is good
git bisect bad                      # this commit is bad
# Repeat until Git identifies the first bad commit

git bisect reset                    # return to original HEAD when done

# Automated bisect with a test script
git bisect start HEAD v1.0
git bisect run pytest tests/test_login.py   # script returns 0=good, non-0=bad
```

### git blame

```bash
git blame file.txt                  # who changed each line + when
git blame -L 10,20 file.txt         # only lines 10–20
git blame -w file.txt               # ignore whitespace changes
git blame -M file.txt               # detect moved lines within file
git blame -C file.txt               # detect copied lines from other files
git blame --since="1 year ago" file.txt
git blame abc1234 -- file.txt       # blame at a specific commit
```

### git grep

```bash
git grep "TODO"                     # search tracked files
git grep -n "TODO"                  # with line numbers
git grep -i "todo"                  # case-insensitive
git grep -c "TODO"                  # count matches per file
git grep -l "TODO"                  # filenames only
git grep -r "TODO" HEAD~5           # search at a past commit
git grep "function\s" -- "*.js"     # with filename pattern
git grep --and -e "TODO" -e "FIXME" # match multiple patterns
```

### git archive (export snapshot)

```bash
git archive --format=zip HEAD > snapshot.zip
git archive --format=tar.gz --prefix=project/ HEAD > project.tar.gz
git archive --format=zip v1.0 > release-v1.0.zip
git archive --format=zip HEAD src/ > src-only.zip  # specific path
```

### git submodules

```bash
# Add submodule
git submodule add https://github.com/user/lib.git libs/mylib
git submodule add -b stable https://github.com/user/lib.git libs/mylib

# Clone repo with submodules
git clone --recurse-submodules https://github.com/user/repo.git
# Or after cloning:
git submodule init
git submodule update
git submodule update --init --recursive  # nested submodules

# Update submodules to latest
git submodule update --remote            # update to branch HEAD
git submodule update --remote --merge    # merge changes
git submodule foreach git pull           # pull in each submodule

# Remove submodule
git submodule deinit libs/mylib
git rm libs/mylib
rm -rf .git/modules/libs/mylib
```

### git worktree (multiple working dirs)

```bash
git worktree add ../hotfix hotfix-branch   # checkout branch in different folder
git worktree add -b new-feat ../new-feat   # create branch + worktree
git worktree list                           # list all worktrees
git worktree remove ../hotfix              # remove worktree
git worktree prune                         # clean up stale worktrees
```

---

## Commit Reference Syntax

```bash
HEAD                    # current commit
HEAD~1  or  HEAD~       # 1 commit back
HEAD~3                  # 3 commits back
HEAD^                   # first parent (same as HEAD~1)
HEAD^2                  # second parent (merge commit)
HEAD^^                  # grandparent (same as HEAD~2)
abc1234                 # by commit hash (full or partial)
v1.0                    # by tag
main                    # tip of branch
origin/main             # tip of remote branch
@{-1}                   # previous branch
@{upstream}             # upstream of current branch
@{u}                    # shorthand for @{upstream}
HEAD@{5}                # HEAD 5 moves ago (from reflog)
HEAD@{2.days.ago}
HEAD@{yesterday}
main@{1 week ago}
```

---

## Hooks

Hooks are scripts in `.git/hooks/` that fire on specific Git events.

```bash
# Client-side hooks
pre-commit            # before commit message — run linters/tests
                      # exit non-zero to abort commit
prepare-commit-msg    # before editor opens — modify default message
commit-msg            # validate commit message format
post-commit           # after commit — notifications
pre-rebase            # before rebase — can abort
post-checkout         # after checkout/switch
post-merge            # after merge
pre-push              # before push — run tests; exit non-zero to abort
post-rewrite          # after rebase or amend

# Server-side hooks
pre-receive           # before any refs are updated — can reject push
update                # per-branch version of pre-receive
post-receive          # after all refs updated — deploy, notify
post-update           # older version of post-receive
```

```bash
# Example: pre-commit hook — run tests before every commit
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh
echo "Running tests..."
pytest --tb=short -q
if [ $? -ne 0 ]; then
    echo "Tests failed — commit aborted"
    exit 1
fi
EOF
chmod +x .git/hooks/pre-commit

# Skip hooks
git commit --no-verify
git push --no-verify

# Shareable hooks: store in repo and symlink
mkdir .githooks
git config core.hooksPath .githooks   # point git to custom hooks dir
```

---

## Workflows

### Gitflow

```
main      ← production releases (tags)
develop   ← integration branch
feature/* ← new features (branch from develop, merge to develop)
release/* ← release prep (branch from develop, merge to main + develop)
hotfix/*  ← urgent fixes (branch from main, merge to main + develop)
```

```bash
# Feature
git switch -c feature/login develop
# ... work ...
git switch develop
git merge --no-ff feature/login
git branch -d feature/login

# Release
git switch -c release/1.2 develop
# ... bump version, fix bugs ...
git switch main;    git merge --no-ff release/1.2; git tag v1.2
git switch develop; git merge --no-ff release/1.2
git branch -d release/1.2

# Hotfix
git switch -c hotfix/1.2.1 main
# ... fix ...
git switch main;    git merge --no-ff hotfix/1.2.1; git tag v1.2.1
git switch develop; git merge --no-ff hotfix/1.2.1
git branch -d hotfix/1.2.1
```

### GitHub Flow (simpler)

```
main  ← always deployable
feature/* ← branch, PR, squash/merge to main, delete branch
```

```bash
git switch -c feature/my-feature main
# ... work, commit ...
git push -u origin feature/my-feature
# Open Pull Request on GitHub
# After review + merge:
git switch main; git pull
git branch -d feature/my-feature
git push origin --delete feature/my-feature
```

### Trunk-Based Development

```
main  ← single trunk; small short-lived branches; feature flags
```

---

## Useful Aliases

Add to `~/.gitconfig` under `[alias]`:

```ini
[alias]
    st   = status -sb
    co   = checkout
    sw   = switch
    br   = branch -vv
    cm   = commit -m
    ca   = commit --amend --no-edit
    undo = reset --soft HEAD~1
    nuke = reset --hard HEAD
    lg   = log --oneline --graph --decorate --all
    ll   = log --oneline --stat
    lp   = log -p
    last = log -1 HEAD --stat
    who  = shortlog -sn --no-merges
    df   = diff
    ds   = diff --staged
    rb   = rebase -i
    pf   = push --force-with-lease
    aliases = config --get-regexp alias
    # show all branches sorted by last commit
    recent = branch --sort=-committerdate --format='%(refname:short) %(committerdate:relative)'
    # clean merged branches
    gone  = "!git fetch -p && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -d"
    # find commits by message
    find  = "!f() { git log --all --oneline --grep=\"$1\"; }; f"
    # count changed files
    changed = diff --name-only HEAD
```

---

## Inspection Utilities

```bash
# Object inspection
git cat-file -t abc1234             # type: blob, tree, commit, tag
git cat-file -p abc1234             # print object content
git ls-tree HEAD                    # list tree (files + dirs in commit)
git ls-tree -r HEAD                 # recursive
git ls-files                        # list tracked files
git ls-files --others               # untracked files
git ls-files --deleted              # deleted files
git ls-files -m                     # modified files

# Count objects / repository stats
git count-objects -v                # objects + disk usage
git rev-list --count HEAD           # total commit count
git rev-list --count main..feature  # commits ahead

# Verify repository integrity
git fsck                            # check object database
git gc                              # garbage collect / compress
git gc --aggressive                 # more thorough (slower)
git prune                           # remove unreachable objects
git pack-refs                       # pack branch refs for efficiency

# Find large files
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  awk '/^blob/ {print substr($0,6)}' | sort -k2 -n -r | head -20
```

---

## Quick Reference Card

```
Setup           config --global user.name/email   init   clone [--depth 1]
Snapshot        add [-p] [-u]   commit [-m] [--amend]   restore [--staged]
History         log [--oneline] [--graph] [--all]   diff [--staged]   show
Branches        branch   switch -c   merge [--no-ff] [--squash]   rebase [-i]
Undo            restore   reset [--soft|--mixed|--hard]   revert   reflog
Remote          remote add   fetch [--prune]   pull [--rebase]   push [-u] [--force-with-lease]
Stash           stash [push -m]   stash list   stash pop   stash drop
Tags            tag -a v1.0 -m   push --tags   tag -d   push --delete
Advanced        cherry-pick   bisect   blame   grep   archive   worktree
Refs            HEAD~n   HEAD^   @{upstream}   @{-1}   HEAD@{n}   reflog
Hooks           pre-commit   commit-msg   pre-push   post-receive   core.hooksPath
Workflows       Gitflow (main+develop+feature+release+hotfix)
                GitHub Flow (main+feature PR)
                Trunk-Based (main + short-lived branches + feature flags)
```

---

*Covers Git 2.35+. Docs: https://git-scm.com/doc*
