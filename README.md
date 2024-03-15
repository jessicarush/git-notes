# Git Notes

These are some very brief and imperfect notes on working with git and github. For more thorough information see:

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Complete list of git commands](https://git-scm.com/docs )
- [How to teach Git](https://rachelcarmena.github.io/2018/12/12/how-to-teach-git.html)
- [Git explained with D3](https://onlywei.github.io/explain-git-with-d3/)


## Table of Contents

<!-- toc -->

- [Basic commands](#basic-commands)
- [Fetch and Merge](#fetch-and-merge)
- [Reset, Checkout, Revert](#reset-checkout-revert)
  * [Reset](#reset)
  * [Checkout](#checkout)
    + [If you want to look at all the files](#if-you-want-to-look-at-all-the-files)
    + [If you want to look at a single file](#if-you-want-to-look-at-a-single-file)
    + [Alternate syntax:](#alternate-syntax)
  * [Revert](#revert)
- [Branch](#branch)
- [Setting the upstream branch](#setting-the-upstream-branch)
- [Rebase](#rebase)
- [Stashing](#stashing)
- [Force Push and Pull](#force-push-and-pull)
- [.gitignore](#gitignore)
- [Commit messages](#commit-messages)
  * [Categories](#categories)
  * [Amending commit messages](#amending-commit-messages)
  * [Summary](#summary)
- [Git and Github identity information](#git-and-github-identity-information)
- [Git remote origin on a flash key](#git-remote-origin-on-a-flash-key)
- [A Specific Process Example](#a-specific-process-example)
  * [Working to create a single commit pull-request-ready branch](#working-to-create-a-single-commit-pull-request-ready-branch)
  * [When someone else is committing to master](#when-someone-else-is-committing-to-master)
  * [Testing/editing locally and on a Raspberry Pi](#testingediting-locally-and-on-a-raspberry-pi)
- [Miscellaneous commands](#miscellaneous-commands)
- [GitHub code search](#github-code-search)

<!-- tocstop -->

## Basic commands

In the command line, make sure you're in the directory you want to repo first.

command | description
------- | -----------
`git init` | creates a new repo  
`git add -A` | adds all files, (new, modified and deleted) to the staging area  
`git add .` | adds all files, (new, modified) to the staging area  
`git add filename.txt` | adds a specific file to the staging area  
`git status` | shows you the staging area  
`git reset filename.txt` | remove a staged file  
`git commit` | takes you to your editor to write a commit comment. Once you save and close the file, the commit will complete  
`git commit -m 'My comments'` | commits right away using the commit comments in quotes  
`git commit --amend` | lets you include a file you forgot to include in the last commit (provided it hasn't been pushed yet). You need to add the file first with `git add filename.txt`. You can also run this command alone to make changes to your last commit message (again, provided it hasn't been pushed yet).  
`git diff` | will show a basic diff of the modified files in the working directory  
`git diff --staged` | show changes of a file in the staged area  
`git diff <commit>^!` | show changes in a commit  
`git difftools` | to look into - will do a diff using a better tool  
`git log` | shows your commit log  
`git log --oneline` | shows your commit log formatted as one line, with a shortened commithash  
`git log --pretty=oneline` | shows your commit log formatted as one line with full length commithash  
`git push` | pushes data from the local repository to the remote repository  
`git fetch` | fetches data from the remote repository to the local repository  
`git merge` | merges the data from the local repository into the working directory  
`git pull` | (fetch + merge) fetches data from the remote repository to the local repository and then merges it to the working directory  
`git remote -v`| show remote repository


## Fetch and Merge

As noted above `fetch` and `merge` are two separate steps that make up a `pull`. Most of the time `pull` is all you need but doing these steps separately allows you to:

View a commits diffs before merging, for example:

```bash
git fetch
From https://github.com/jessicarush/git-notes
   8b684ad..4f87800  master     -> origin/master

git diff 4f87800^!
git merge
```


## Reset, Checkout, Revert

These three commands will let you temporarily look back at earlier commits, reset to a previous commit, remove commits from the log or revert individual files to previous commit states.


### Reset

Reset is the simplest if you want to revert all your files to an earlier commit and/or remove commits from your commit history. You have two main options using reset:

`git reset [commit]` –– this uses the default mode `--mixed` which removes all commits after [commit] in the history but preserves any changes made to your local files. If you do a `git status`, it will show as *changes not staged for commit*. As mentioned above `git reset filename.txt` is also a quick way to remove a file from the staging area.

`git reset --soft [commit]` –– is the same as above except all changes since [commit] will be staged and waiting for a new commit.

`git reset --hard [commit]` –– **BE CAREFUL** removes all commits after [commit] AND reverts local files back to what they were at the specified [commit]

`git reset --hard HEAD~1` –– **BE CAREFUL** goes back one commit.

If you're not sure which commit you want to reset to, use `git checkout` first. There are also mode flags `--merge` and `--keep`: [see the docs](https://git-scm.com/docs/git-reset).


### Checkout

While this is mainly used for switching branches, `checkout` also lets you look at your files as they were at a particular [commit]. You can *check out* all your files or just a single file. The process is a little different depending on which you're doing. Note that unlike `reset`, checkout does not remove any commits from the commit history.

#### If you want to look at all the files

`git checkout [commit]`  –– temporarily reverts all local files to [commit]  

At this point if you look at the actual files in your working directory you'll see that  they're rolled back to [commit] but know this state is not permanent. You are in what's called a *detached head state*. You can do some things in this state, including running the code, test changes, checking out other commit states but ultimately, when you're done, you'll most likely do one of two things:

1. go back to your most recent commit like so:  `git checkout master`  –– this puts your head (and files) back to the end of the master branch. Once here, you may then decide you want to `git reset` or `git revert`.

2. create a new *branch* from this previous commit state with the intention of continuing to make changes and eventually merging back into the master branch. It might look something like this:

```bash
git checkout [commit]
git checkout -b mybranch
...making changes...
git add -A
git commit -m 'Added mybranch features'
git checkout master
git merge mybranch
```

Depending on your changes, you will likely have merge conflicts so you'll have to resolve them carefully before moving on. For this reason, consider whether you want to keep those later commits in the master branch that you moved in front of. It may make more sense to reset, then branch off.

#### If you want to look at a single file

`git checkout [commit] filename.txt`  –– reverts a file to [commit]

The difference here when we just checkout a single file, is that we're NOT in a *detached head state* meaning it's pretty much business as usual. Your file has been reverted back to a previous commit so at this point you can do one of two things:

1. choose to keep the file at this earlier commit by doing a `git add` and a `git commit` just as if you were making normal changes to a file. If you look at your log, you'll see all commits are in tact. So, you could always change your mind again.

2. choose to go back to your most recent state by checking out using the most recent commit hash: `git checkout [commit] filename.txt`. If you do a `git status` you should see no changes.

#### Alternate syntax:

Note that when indicating a commit to checkout to, you can also use this syntax:

`git checkout HEAD~2`  –– checkout two parent commits ago  
`git checkout HEAD~2 filename.txt`  –– checkout a file from two parent commits ago  

You can also use `^` to differentiate between merged branch parents, but tbh I find it a little confusing. [Here's an explanation](http://www.paulboxley.com/blog/2011/06/git-caret-and-tilde) but I'm sticking with commit hashes.


### Revert

The git revert command is a forward-moving undo operation that offers a safe method of undoing changes. Instead of deleting or orphaning commits in the commit history, a revert will create a new commit that inverses the changes specified. Git revert is a safer alternative to git reset in regards to losing work.

`git revert [commit]`

When you run this command, your editor will open up for a commit message. You can leave it at the default which is 'revert: commit message'. Here's an example:

```bash
git revert 13978d2
git log --oneline

e679e71 Revert "Add three"
13978d2 Add three
0524870 Add two
d5d461c Add one
a114858 Initial commit
```


## Branch

Create a new branch and switch over to it:

```bash
git branch new_features
git checkout new_features
```

Make your changes as you normally would, `add` and `commit` as normal.
To push your new branch to your remote repo:

```bash
git push -u origin new_features
```

If you want to rename a branch:

```bash
git branch -m old-name new-name
git push origin --delete old-name
git push origin new-name
```

When ready to merge your branch into the master branch:

```bash
git checkout master
git merge new_features
git push
```

To list all your branches (local & remote):

```bash
git branch -a
```

To delete a local branch:

```bash
git branch -d branch_name
```

To delete a remote branch:

```bash
git push origin --delete branch_name
```

**Summary:**

`git branch refactoring`  –– creates a new branch called 'refactoring'  
`git checkout refactoring`  –– moves over to the new branch and updates the working directory  
`git checkout -b refactoring`  –– creates a new branch and move over to it in one step.  
`git push --set-upstream origin refactoring`  –– add your new branch to the the remote repo  
`git pull origin refactoring`  –– to pull from a branch  
`git branch`  –– display the names of all the branches (the starred one is your current one)  
`git merge refactoring`  –– merge the branch into the branch you are currently in (for example: `git checkout master` first)


## Setting the upstream branch 

Note that in the command used above `git push -u <remote> <branch>`, we are actually setting the upstream branch to `<remote>` in addition to pushing.

Upstream branches are useful because:

- You get references to your remote repositories and you essentially know if you are ahead of them or not. When performing a “git fetch” command, you can bring the new commits from your remote repository and you can choose to merge them at will.
- You can perform pull and push easily. When you set your upstream (or tracking) branches, you can simply execute pulls and pushes without having to specify the target branch.

To set the upstream:

```bash
git branch -u <remote>/<branch>
```

To check what the upstreams have been set to for each branch:

```bash
git branch -vv
```


## Rebase

coming soon...

`git pull --rebase`  –– instead of performing a regular `fetch` + `merge`, this will do a `fetch` + `rebase`


## Stashing

If you have un-staged, un-committed changes that you want to "save for later", you can stash them.

```bash
git stash save "my description"
```

To list your saved stashes:

```bash
git stash list
```

To show the diff of the most recent stash before applying:

```bash
git stash show -p
```

To show the diff of a specific stash before applying:

```bash
git stash show -p stash@{1}
```

To retrieve at stash:

```bash
git stash apply stash@{0}
```

The stash will still be saved though. If you want to remove it from the saved list:

```bash
git stash drop stash@{0}
```

To remove all the saved stashes:

```bash
git stash clear
```

To apply and remove the last stash:

```bash
git stash pop
```


## Force Push and Pull

To force a pull (and overwrite local diffs):

```bash
git fetch --all
git reset --hard origin/master
```

To force a push (and overwrite remote diffs)

```bash
git push origin <your_branch_name> --force
```


## .gitignore

To have git ignore files or folders, create a file in the project root directory (where your `.git` folder lives) called `.gitignore` – then add a line for each file or folder you want to ignore, for example:

```bash
# https://git-scm.com/docs/gitignore
# https://help.github.com/articles/ignoring-files/

# dependencies
/node_modules

# bytecode compiled
__pycache__/

# environments
.env
/venv

# logs
/logs

# database
app.db

# misc
.DS_Store

# project specific
working_files
```

Note that:

```bash
# ignore all .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the venv dir/file in the current directory, not subdir/venv
/venv

# ignore all files in any directory named __pycache__
__pycache__/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```

Note if you add a new file to your git ignore that was previously being tracked, you will need to remove it from the index:

```bash
git rm --cached <file>
```

If you want to remove a whole directory, you need to remove all files in it recursively:

```bash
git rm -r --cached <directory>
```

To learn more about the patterns that can be used for `.gitignore` files see:

- (GitHub ignoring files)[https://docs.github.com/en/get-started/getting-started-with-git/ignoring-files]
- (Pro git book)[https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#_ignoring]
- (Git man pages)[https://git-scm.com/docs/gitignore]


## Commit messages

The preferred method of writing commit messages is:

```bash
Capitalized, short (50 chars or less) summary

More detailed explanation, if necessary. Keep line length to about 72
characters. Write your commit message in the imperative: "Fix bug" and
not "Fixed bug" or "Fixes bug."  This convention matches up with commit
messages generated by commands like git merge and git revert. Explain
your solution and why you're doing it, as opposed to describing what
you're doing.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet,
   preceded by a single space, with blank lines in
   between, but conventions vary here
```

Note that keeping the first subject line to 50 chars or less works well with: `git log --pretty=oneline`

Some further recommendations for writing commit messages:

[How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)  
[Commit Message Guidelines](https://gist.github.com/abravalheri/34aeb7b18d61392251a2)  

### Categories

Some projects also include a short reference word at the start of the summary line to indicate the type of change. An example of this is seen in the [pandas repo](https://github.com/pandas-dev/pandas).

An example system I could get behind:

**Feature:** a new feature  
**Fix:** a bug fix  
**Docs:** documentation changes only  
**Style:** changes that do not affect the meaning of the code (e.g. white-space, formatting, etc)  
**Refactor:** a code change that neither fixes a bug nor adds a feature  
**Perf:** a code change that improves performance  
**Test:** adding or modifying tests  
**Update:** changes related to updating external tools/libraries  

For my notes & examples repos, I'm also going to use:

**Add:** new examples, explanations or material added.  
**Rename:** a file has been renamed to better reflect the topic  

### Amending commit messages

If you made a mistake in your last commit message and you haven't pushed yet, you can use the --amend flag noted above. If you notice mistakes in old commit messages do this:

1. Use `git rebase -i HEAD~n` command to display a list of the last *n* commits in your default text editor.
2. For each commit message you want to edit, change the word *pick* to *reword* then save and close the file.
3. This will then open each commit message file for you to edit and save.
4. When it's all done, force push the amended commits with `git push --force`

Be aware that amending the commit message will result in a new commit with a new ID. For more information see [Github's article](https://help.github.com/articles/changing-a-commit-message/.)

### Summary

- Separate subject from body with a blank line
- Limit the subject line to 50 characters
- Capitalize the subject line
- Do not end the subject line with a period
- Use the imperative mood in the subject line
- Wrap the body at 72 characters
- Use the body to explain what and why vs. how


## Git and Github identity information

Add your Github name and email address (note: your email here must match the primary one you have set on your Github account in order for your commits to register on their heatmap):

```bash
git config --global user.email "email@example.com"
git config --global user.name "Your Name"
```

To confirm that you have set the email address correctly:

```bash
git config --global user.email
email@example.com
```

If you want to change your name and email for a local repository only, navigate to the directory containing the git directory:

```bash
git config user.email "email@example.com"
git config user.name "Your Name"
```

To confirm that you have set the email address correctly:

```bash
git config user.email
email@example.com
```

To show the remote repository connected to your current working directory:

```bash
git remote show origin
```

To create a clone of a github repository:

```bash
git clone https://github.com/USERNAME/REPOSITORY
```

If you've added your public SSH keys to your github account:

```bash
git clone git@github.com:USERNAME/REPOSITORY
```

To link an existing local repo to a github repo:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

If you've added your public SSH keys to your github account:

```bash
git remote add origin git@github.com:USERNAME/REPOSITORY.git
```

Note: if you want to change the origin, edit the config file in the `.git` directory or you can run the command to delete the old origin, then add the new one with above command:

```bash
git remote remove origin
```

Be sure to push to your master branch after changing the remote:

```bash
git push -u origin master
```

The above requires that you have a master branch already created on
github. If not you'll need to enter the following. You'll be prompted for a username and password (unless you set up SSH).

```bash
git push --set-upstream origin master
```

Finally, if you are trying to interact with a github repo and are still being asked to enter your username and password, you probably need to change the remote URL from HTTPS to SSH. To do this first check if you are using HTTPS:

```bash
git remote -v
> origin  https://github.com/USERNAME/REPOSITORY.git (fetch)
> origin  https://github.com/USERNAME/REPOSITORY.git (push)
```

Change your remote's URL from HTTPS to SSH with the `git remote set-url` command.

```bash
git remote set-url origin git@github.com:USERNAME/REPOSITORY.git
```

Verify that it's changed:

```bash
git remote -v
> origin  git@github.com:USERNAME/REPOSITORY.git (fetch)
> origin  git@github.com:USERNAME/REPOSITORY.git (push)
```

You can test that SSH is set up properly to Github with this command:

```bash
ssh -T git@github.com
```


## Git remote origin on a flash key

- git init, add and commit a repo in your local directory
- cd into the USB flash key (e.g. `cd /Volumes/flash_key_name/Python`)
- `git init --bare myrepo.git`
- cd back to local repo
- `git push --set-upstream /Volumes/flash_key_name/Python/myrepo.git/ master`
- `git remote add usb /Volumes/flash_key_name/Python/myrepo.git`
- `git push usb master`

To clone that repo somewhere else:

```bash
git clone /Volumes/flash_key_name/Python/myrepo.git
```


## A Specific Process Example

Different organizations will have their own preferred methods of managing git logs. This example is one such method aimed at doing pull requests of 1 commit.

### Working to create a single commit pull-request-ready branch 

- `git fetch`
- `git checkout origin/master`
- `git checkout -b CP-123-wip`
- `git branch -u origin/master`

Do work and commit as much as you want (no pushing unless you want a backup), then:

- `git pull`
- `git status` *(ahead by x commits)*
- `git rebase -i HEAD~x`

> Note if you were ahead by more than one commit, the rebase command will prompt you amend the commit message so that you can write it in the organizations preferred format e.g. `CP-123: This is my formal commit message`. If you were only ahead by 1 commit in the first place, don't bother rebasing but *do* amend your last commit message if necessary using `git commit --amend`.

> In the interactive rebase pick the first one and squash the rest, the second step of the rebase will allow you to merge and modify the commit message.

- `git status` *(ahead by 1 commit)*
- `git push origin HEAD:CP-123`

### When someone else is committing to master

In the above situation if the master branch has some features merged while you are working on your branch then you will need to pull down the new stuff and possibly resolve any conflicts before cherry-picking your work into a new commit:

- `git fetch`
- `git checkout origin/master`
- `git checkout -b CP-123-wip`
- `git branch -u origin/master`

Do work and commit as much as you want (no pushing unless you want a backup), then:

- `git checkout master`
- `git pull`
- `git checkout -b CP-123-wip`
- `git status` *(diverdged, x commits on master and x commits on branch)*
- `git rebase -i HEAD~x` *(x is number of commits on branch)*
- `git status` *(diverdged, x commits on master and 1 commit on branch)*

At this point, note the commit hash of the rebased branch. Then:

- `git checkout -b CP-123`
- `git branch -u origin/master`
- `git cherry-pick <hash>`

At this point you may have conflicts. Resolve them by doing:

- `git status`

Open the files and manually resolve any conflict sections:

```
<<<<<<< HEAD
open an issue
=======
ask your question in IRC.
>>>>>>> branch-a
```

Once you've resolves a file, save it and add it (`git add filename`). When all files are added, commit.

Your new branch should now be one commit off the current master branch. 

- `git push origin HEAD:CP-123`

### Testing/editing locally and on a Raspberry Pi

Assuming I've created a local branch using:

```bash
git checkout -b CP-123-wip
```

And I'm following the master branch:

```bash
git branch -u origin/master
```

If I want to test my current local branch's code on a raspberry pi:

```bash
git push origin HEAD:CP-123-wip
```

On the raspberry pi, **clone the repository**, then:

```bash
git checkout origin/CP-123-wip
```

If I want to make changes there on the pi, commit as normal then when ready:

```bash
git push origin HEAD:CP-123-wip
```

Now locally you should be able to pull down those changes:

```bash
git pull origin CP-123-wip
```

You can carry on editing back and forth between your local workspace and the pi using those two commands. When you're ready for a pull request, continue as normal with rebasing, etc.


## Miscellaneous commands

When copying files from linux or Mac OS to Windows, permissions on files may get changed. Git may register these as changes and all files will show as being modified. If you don't want this use the following command:

```bash
git config core.filemode false
```

or

```bash
git config --global core.filemode false
```

To print git log with author, date, commit message:

```bash
git log --pretty=format:"%h%x09%an%x09%ad%x09%s"
```

Gives some detail into the files that were modified:

```bash
git whatchanged 
```

Show the amount each file was changed:

```bash
git log --stat 
```

`git diff --stat <sha1> <sha2>` gives the files and the amount of changes between two commits.

`git diff --stat <branch>` to compare to another branch (e.g. master)

Compare Two Git Branches:

```bash
git diff <branch>...<branch> --ignore-all-space
```

To rename `main` to `master`:

```bash
git branch -m main master
git push -u origin master
```

If you already have a github repo, you'll want to go into the repo settings and manually set master as the default branch then delete the main branch.

## GitHub code search 

GitHIb now includes all kinds of syntax for specific code searches. Here are some examples:

search query | description
------------ | -----------
kysely path:**/package.json | search for `kysely` in any `package.json`.

