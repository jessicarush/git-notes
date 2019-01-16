# Git Notes

These are some very brief and imperfect notes on working with git and github. For more thorough information see:

[How to teach Git](https://rachelcarmena.github.io/2018/12/12/how-to-teach-git.html) *- really good!*  
[Complete list of git commands](https://git-scm.com/docs ).  
[Git explained with D3](https://onlywei.github.io/explain-git-with-d3/).  

## Table of Contents

<!-- toc -->

- [Basic commands](#basic-commands)
- [Reset, Checkout, Revert](#reset-checkout-revert)
  * [Reset](#reset)
  * [Checkout](#checkout)
    + [If you wan to look at all the files:](#if-you-wan-to-look-at-all-the-files)
    + [If you want to look at a single file:](#if-you-want-to-look-at-a-single-file)
    + [Alternate syntax:](#alternate-syntax)
  * [Revert](#revert)
- [Branch](#branch)
- [.gitignore](#gitignore)
- [Commit messages](#commit-messages)
  * [Amending commit messages](#amending-commit-messages)
- [Git remote origin on a flash key](#git-remote-origin-on-a-flash-key)
- [Git & Github](#git--github)

<!-- tocstop -->

## Basic commands

In the command line, make sure you're in the directory you want to repo first.

`git init`  –– creates a new repo  
`git add -A`  –– adds all files, (new, modified and deleted) to the staging area  
`git add .`  –– adds all files, (new, modified) to the staging area  
`git add filename.txt`  –– adds a file to the staging area  
`git status`  –– shows you the staging area  
`git reset filename.txt`  –– remove a staged file  
`git commit`  –– takes you to your editor to write a commit comment. Once you save and close the file, the commit will complete  
`git commit -m 'My comments'`  –– commits right away using the commit comments in quotes  
`git commit --amend`  –– opens an editor to allow you to make changes to your last commit message (not pushed to github yet).  
`git diff`  –– will show a basic diff of the modified files in the working directory  
`git diff --staged`  –– show changes of a file in the staged area  
`git diff <commit>^!`  –– show changes in a commit  
`git difftools`  –– to look into - will do a diff using a better tool  
`git log`  –– shows your commit log  
`git log --oneline`  –– shows your commit log formatted as one line, with a shortened commithash  
`git log --pretty=oneline`  –– shows your commit log formatted as one line with full length commithash  
`git push`  –– pushes data from the local repository to the remote repository  
`git fetch`  –– fetches data from the remote repository to the local repository  
`git merge`  –– merges the data from the local repository into the working directory  
`git pull`  –– (fetch + merge) fetches data from the remote repository to the local repository and then merges it to the working directory  


## Reset, Checkout, Revert

These three commands will let you temporarily look back at earlier commits, reset to a previous commit, remove commits from the log or revert individual files to previous commit states.


### Reset

Reset is the simplest if you want to revert all your files to an earlier commit and/or remove commits from your commit history. You have two main options using reset:

`git reset [commit]`  –– this uses the default mode `--mixed` which removes all commits after [commit] in the history but preserves any changes made to your local files. If you do a `git status`, it will show as *changes not staged for commit*. As mentioned above `git reset filename.txt` is also a quick way to remove a file from the staging area.  

`git reset --soft [commit]`  –– is the same as above except all changes since [commit] will be staged and waiting for a new commit.  

`git reset --hard [commit]`  –– **BE CAREFUL** removes all commits after [commit] AND reverts local files back to what they were at the specified [commit]

If you're not sure which commit you want to reset to, use `git checkout` first. There are also mode flags `--merge` and `--keep`: [see the docs](https://git-scm.com/docs/git-reset).


### Checkout

While this is mainly used for switching branches, `checkout` also lets you look at your files as they were at a particular [commit]. You can *check out* all your files or just a single file. The process is a little different depending on which you're doing. Note that unlike `reset`, checkout does not remove any commits from the commit history.

#### If you wan to look at all the files:

`git checkout [commit]`  –– temporarily reverts all local files to [commit]  

At this point if you look at the actual files in your working directory you'll see that  they're rolled back to [commit] but know this state is not permanent. You are in what's called a *detached head state*. You can do some things in this state, including running the code, test changes, checking out other commit states but ultimately, when you're done, you'll most likely do one of two things:

1. go back to your most recent commit like so:  `git checkout master`  –– this puts your head (and files) back to the end of the master branch. Once here, you may then decide you want to `git reset` or `git revert`.

2. create a new *branch* from this previous commit state with the intention of continuing to make changes and eventually merging back into the master branch. It might look something like this:
```
git checkout [commit]
git checkout -b mybranch
...making changes...
git add -A
git commit -m 'Added mybranch features'
git checkout master
git merge mybranch
```

Depending on your changes, you will likely have merge conflicts so you'll have to resolve them carefully before moving on. For this reason, consider whether you want to keep those later commits in the master branch that you moved in front of. It may make more sense to reset, then branch off.

#### If you want to look at a single file:

`git checkout [commit] filename.txt`  –– reverts a file to [commit]  

The difference here when we just checkout a single file, is that we're NOT in a *detached head state* meaning it's pretty much business as usual. Your file has been reverted back to a previous commit so at this point you can do one of two things:

1. choose to keep the file at this earlier commit by doing a `git add` and a `git commit` just as if you were making normal changes to a file. If you look at your log, you'll see all commits are in tact. So, you could always change your mind again.

2. choose to go back to your most recent state by checking out using the most recent commit hash: `git checkout [commit] filename.txt`. If you do a `git status` you should see no changes.

#### Alternate syntax:

Note that when indicating a commit to checkout to, you can also use this syntax:  

`git checkout HEAD~2`  –– checkout two parent commits ago  
`git checkout HEAD~2 filename.txt`  –– checkout a file from two parent commits ago  

You can also use `^` to differentiate between merged branch parents, bit tbh I find it to be a little confusing. [Here's an explanation](http://www.paulboxley.com/blog/2011/06/git-caret-and-tilde) but I'm sticking with commit hashes.


### Revert

The git revert command is a forward-moving undo operation that offers a safe method of undoing changes. Instead of deleting or orphaning commits in the commit history, a revert will create a new commit that inverses the changes specified. Git revert is a safer alternative to git reset in regards to losing work.

`git revert [commit]`

When you run this command, your editor will open up for a commit message. You can leave it at the default which is 'revert: commit message'. Here's an example:

```
git revert 13978d2
git log --oneline

e679e71 Revert "Add three"
13978d2 Add three
0524870 Add two
d5d461c Add one
a114858 Initial commit
```


## Branch

`git branch refactoring`  –– creates a new branch called 'refactoring'  
`git checkout refactoring`  –– moves over to the new branch and updates the working directory  
`git checkout -b refactoring`  –– creates a new branch and move over to it in one step.  
`git push --set-upstream origin refactoring`  –– to add your new branch to the the origin (github)  
`git pull origin refactoring`  –– to pull from a branch  
`git branch`  –– display the names of all the branches  
`git merge refactoring`  –– merge the branch into the branch you are currently in (for example: `git checkout master` first)


## .gitignore

To have git ignore files or folders, create a file in the project root directory (where your `.git` folder lives) called `.gitignore` – then add a line for each file or folder you want to ignore
for example:
```
__pycache__
.DS_Store
venv
.env
working_files
```


## Commit messages

The preferred method of writing commit messages is:
```
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

Highlights:
- Separate subject from body with a blank line
- Limit the subject line to 50 characters
- Capitalize the subject line
- Do not end the subject line with a period
- Use the imperative mood in the subject line
- Wrap the body at 72 characters
- Use the body to explain what and why vs. how

Some projects also include a short reference word at the start of the summary line to indicate the type of change. An example of this is seen in the [pandas repo](https://github.com/pandas-dev/pandas).

An example system I could get behind:

**Feature:** A new feature  
**Fix:** A bug fix  
**Docs:** Documentation changes only  
**Style:** Changes that do not affect the meaning of the code (e.g. white-space, formatting, etc)  
**Refactor:** A code change that neither fixes a bug nor adds a feature  
**Perf:** A code change that improves performance  
**Test:** Adding or modifying tests  
**Update:** Changes related to updating external tools/libraries  

For my repos that are notes & examples I'm also going to use:

**Add:** New examples, explanations or material added.  
**Rename:** A file has been renamed to better reflect the topic  


### Amending commit messages

If you made a mistake in your last commit message and you haven't pushed yet, you can use the --amend flag noted above. If you notice mistakes in old commit messages do this:

1. Use `git rebase -i HEAD~n` command to display a list of the last *n* commits in your default text editor.
2. For each commit message you want to edit, change the word *pick* to *reword* then save and close the file.
3. This will then open each commit message file for you to edit and save.
4. When it's all done, force push the amended commits with `git push --force`

Be aware that amending the commit message will result in a new commit with a new ID. For more information see [Github's article](https://help.github.com/articles/changing-a-commit-message/.)


## Git remote origin on a flash key

- git init, add and commit a repo in your local directory
- cd into the USB key (e.g. `cd /Volumes/Squirrel/Python`)
- `git init --bare myrepo.git`
- cd back to local repo
- `git push --set-upstream /Volumes/NUT/Python/myrepo.git/ master`
- `git remote add usb /Volumes/NUT/Python/myrepo.git`
- `git push usb master`

To clone that repo somewhere else:
```
git clone /Volumes/NUT/Python/myrepo.git
```




## Git & Github

Note that your commits won't register on Github's heatmap unless you're using the same email address. To set up your commit name and email address:  
```
git config --global user.email "email@example.com"
git config --global user.name "Your Name"
```

To confirm that you have set the email address correctly:
```
git config --global user.email
email@example.com
```

To show the current remote of a local github repo:
```
git remote show origin
```

To create a clone of a github repo:
```
git clone https://github.com/USERNAME/REPOSITORY
```

To link an existing local repo to github repo:
```
$ git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

If you've added your public SSH keys to your github account:
```
git remote add origin git@github.com:USERNAME/REPOSITORY.git
```

Note, if you want to change the origin, edit the config file in the `.git`
directory or run the command to delete the old origin, then add the new one:
```
git remote remove origin
```

Then push to your master branch:
```
git push -u origin master
```

The above requires that you have a master branch already created on
github. If not you'll need to enter the following. You'll be prompted for a username and password (unless you set up SSH).
```
git push --set-upstream origin master
```

Create a new branch and switch over to it:
```
git branch new_features
git checkout new_features
```

Make your changes as you normally would.
Git add and commit as normal.
To push your new branch to github:
```
git push --set--upstream origin new_features
```

When ready to merge your branch back into master:
```
git checkout master
git merge new_features
git push
```

To force a pull (and overwrite and local changes):
```
git fetch --all
git reset --hard origin/master
```

To force a push
```
git push origin <your_branch_name> --force
```

If you want to rename a branch:
```
git branch -m old-name new-name
git push origin --delete old-name
git push origin new-name
```

If you forgot to include a file in a commit:
```
git add missed-file.py
git commit --amend
```
