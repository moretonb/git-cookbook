# Every day common git usages

## Introduction
The aim of this document is to walk through the most common working scenarios when working with git.

## Other resources
* comprehensive [cheat sheet](http://cheat.errtheblog.com/s/git)
* [interactive online course](https://learngitbranching.js.org/) on getting started with branching
* official git [documentation](https://git-scm.com/doc)

## Pulling down a repo not already on your machine
Navigate to where you want the parent folder to be, then [clone](https://git-scm.com/docs/git-clone).
```bash
git clone https://some.url
```

## Before beginning a new piece of work - check current status
First check the [status](https://git-scm.com/docs/git-status) of your local repo.
```bash
git status
```
If there is work in flight here decide if you want to keep it, check the contents with a [diff](https://git-scm.com/docs/git-diff).
```bash
git diff
```
Most commonly it will be work from experimentation or something else safe to remove with a [reset](https://git-scm.com/docs/git-reset). You may need to also delete any untracked files/folders and any build artifacts with [clean](https://git-scm.com/docs/git-clean).

For reset:
* --hard resets the index (staged area) and the tracked working tree to its previous commit state

For clean:
* -f clean will refuse deletions if this is not present, can also be done with global configuration
* -d instructs deletion of untracked folders as well as files
* -x makes clean not honour .gitignore, any ignored folders/files will also be removed
```bash
git reset --hard
git clean -xfd
```

## Before beginning work - ensure you are up to date
Ensure your local version of master is up to date with the remote with [checkout](https://git-scm.com/docs/git-checkout) and [pull](https://git-scm.com/docs/git-pull).
```bash
git checkout master
git pull
```

## Beginning work - on a new branch
Just create a new branch and check it out. This can be achieved in one command with [checkout](https://git-scm.com/docs/git-checkout). Try to include the ticket number with your branch name.
```bash
git checkout -b 1234_new_feature_branch
```

## Oops I started work before branching!
Don't panic, this is a common thing to slip people's attention. To fix:
* check the work you've done so far with [status](https://git-scm.com/docs/git-status) and [diff](https://git-scm.com/docs/git-diff)
* [stash](https://git-scm.com/docs/git-stash) your changes (-a includes untracked and ignored files)
* switch to master and ensure you are up to date with [checkout](https://git-scm.com/docs/git-checkout) and [pull](https://git-scm.com/docs/git-pull)
* create and switch to a new branch with checkout
* pop your stash in the new branch
```bash
git status
git diff
git stash -a
git checkout master
git pull
git checkout -b 1234_new_feature_branch
git stash pop
```

## Beginning work - on an existing remote branch
You may not have this branch locally. First check what remote branches you can currently see with [branch](https://git-scm.com/docs/git-branch) (-a includes remote tracking branches and local ones).
```bash
git branch -a
```
If the remote branch is not present you will need to [fetch](https://git-scm.com/docs/git-fetch) down the latest remote tracking information (-all fetches all remotes).
```bash
git fetch --all
```
Then it is just a case of using [checkout](https://git-scm.com/docs/git-checkout) and [pull](https://git-scm.com/docs/git-pull). To switch and ensure you have the latest changes.
```bash
git checkout 1234_existing_branch
git pull
```

## Creating a commit (something like a save point)
As you progress through making changes it is a very good idea to make more than one commit to track your progress. A commit will ideally contain some value towards your current feature .e.g a now passing test. At the very least it should compile. Typically you will also push this up off your machine at the same time to avoid loss.

For this:
* check your work compiles and passes local tests (if appropriate)
* check your [status](https://git-scm.com/docs/git-status) and [diff](https://git-scm.com/docs/git-diff)
* if files are included that you want to be ignored e.g. generated files amend the .gitignore if the project already has one. Otherwise sensible .gitignore files for various languages and IDEs can be found [here](https://github.com/github/gitignore). For C#/visual studio the one to use is [VisualStudio](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore)
* if you want to revert an individual file use [checkout](https://git-scm.com/docs/git-checkout)
* delete any files/folders you don't want manually
* once happy with the changes stage them with [add](https://git-scm.com/docs/git-add) (-A updates the whole index to reflect the current working tree)
* next [commit](https://git-scm.com/docs/git-commit) the changes with a good message including the ticket number (-m specifies the message) e.g. 1234 add git cookbook. Terse but descriptive is the aim of the game.
* finally [push](https://git-scm.com/docs/git-push) the work up to the remote. The first time you push up a new local branch you need to hook up the upstream with the -u option
```bash
git status
(optional revert file)
git checkout ./a/file/path
git diff
git add -A
git commit -m"1234 add git cookbook"
git push
(or with the first push of a new branch)
git push origin 1234_new_feature_branch -u
```

The exception to this rule is if you have a chunk of work not tracked and you are leaving for the day. In this case it is common to create a working commit, push local changes up later amend them with a clean commit. This has the same commit and push flow to above when handling work part way done.

The following day you will want to stage your finished changes and amend your previous commit and forcefully push it up to orphan the old commit. The --amend option on commit will allow you to amend the previous commit details. This may end up with you needing to do a little vi. The -f option on push forces the previous version of the same commit to be replaced/orphaned.

For details on how to setup notepad as the default editor instead see [here](https://stackoverflow.com/questions/10564/how-can-i-set-up-an-editor-to-work-with-git-on-windows).

For a vi cheat sheet see [here](http://www.lagmonster.org/docs/vi.html). You can get a long way with just "i" (insert mode), "esc" (leave insert mode) and ":x" quit and save.
```bash
git status
(optional revert file)
git checkout ./a/file/path
git diff
git add -A
git commit --amend
git push -f
```

## Throw work away
Now you have a save point (a previous good commit) a valid workflow that you sometimes need is one of experimentation. If you want to thrash some ideas out you have the freedom to revert back to your last commit as many times as you like. To do so you spit out some prototype code then revert back using [reset](https://git-scm.com/docs/git-reset) and [clean](https://git-scm.com/docs/git-clean). You may or may not want all of these options with clean, see the earlier paragraph with more detail.
```bash
git reset --hard
git clean -xfd
```

## Getting ready for a pull request
First chat this through with your team if there is more than one of you current working in the same repo. This will avoid you getting into merge hell, you want to limit the number of these required where possible. Once you are ready for your changes to be reviewed you want to ensure you have the latest version of master merged in with your branch before you raise a pull request. This flow will ensure merges back on to master after a pull request are fast forward only. You accomplish this with [merge](https://git-scm.com/docs/git-merge). Before this make sure you have committed and pushed your work so far.
```bash
git checkout master
git pull
git checkout 1234_working_branch
git merge master
git push
```
Push in this case is fine because merge will generate a commit on your behalf.

This flow may break if you have conflicts as a result of the merge from master. If your team is pro at communication you can avoid a lot of these instances but for those you couldn't avoid you will need to resolve them manually. You can accomplish this one of a few ways:
* manually edit the files looking at the descriptors for where each block is from
* use an editor like visual studio that understands how to interpret these diffs
* configure a default diff tool for git which will get started automatically e.g. [meld](http://meldmerge.org/) (make sure you have it installed)

To configure a tool:
```bash
git config --global merge.tool meld
git config --global mergetool.meld.path /c/Program files (x86)/meld/bin/meld
```

Once you have dealt with the merge conflicts you need to stage, commit and push the changes.
```bash
git checkout master
git pull
git checkout 1234_working_branch
git merge master
#it's a trap, there were conflicts
#conflicts resolved
git add -A
git commit -m"1234 bridges master changes in new feature"
git push
```
