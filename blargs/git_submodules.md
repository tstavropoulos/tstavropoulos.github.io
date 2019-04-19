# WTF is a Git Submodule? (2019-04-18)

[Return To Blogs](index.md)

## Introduction

If you have different projects that share any nontrivial amount of common code, especially code like generic tools and utilities, and you use git for source control you should look into a git submodule to share said common code.  Using a submodule is really just telling one git repo "keep this whole other repo here inside you, and don't manage its files".  To use an example out of game development, code representing your core game engine would live in the GameEngine repository, and all of your games built using it would have their own repositories and include GameEngine as a *submodule*.

Git submodules work in a quintessentially *git* way (I'm saying that as a good thing).  When you add a submodule inside a project, you actually just tell the project's repository to checkout a specific *commit* of the submodule repo.  So, what happens if someone pushes new commits to the GameEngine repository and you use `git pull` in your Game repository?  Nothing happens.  *This is good.*  If submodules didn't work this way, they really would be unusable.  So if you make some breaking changes in GameEngine, you can wait to pull those changes into a project until you're ready (if ever).

## Real Life Examples

Below are some commandline examples showing working with Submodules.  They show how easy managing a submodule is (if you use git from commandline, which I highly recommend all programmers be comfortable with).  Below, **PART** is a Unity project and git repository, and **BGC_Tools** is a submodule from [our real, open-source repo of Unity tools](https://github.com/UCRBrainGameCenter/BGC_Tools).

### Getting Started

#### Example 1: Adding a submodule to a repository

Navigate to where you want the submodule, clone the repo, and add it as a submodule.

```shell
~/PART$ cd Assets/Plugins
~/PART/Assets/Plugins$ git clone https://github.com/UCRBrainGameCenter/BGC_Tools.git
Cloning into 'BGC_Tools'...
done.
~/PART/Assets/Plugins$ git submodule add https://github.com/UCRBrainGameCenter/BGC_Tools.git BGC_Tools
Adding existing repo at 'Assets/Plugins/BGC_Tools' to the index
```

Now the submodule has been added.  If we navigate back to the root of the PART project, we can see the changes.

```shell
~/PART/Assets/Plugins$ cd ~/PART
~/PART$ git status
On branch master
Your branch is up to date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   .gitmodules
        new file:   Assets/Plugins/BGC_Tools
```

So it added a `.gitmodules` file, and it represents the addition of the entire repository as a "file".  `add`, `commit`, and `push` your changes like normal.

```shell
~/PART$ git add -A
<snip>
~/PART$ git commit -m "Adding a submodule"
<snip>
~/PART$ git push
<snip>
```

### Dealing with other people's changes

#### Example 2: Pulling in the addition of a submodule

It looks like your ambitious coworker added a submodule to the project in his last push, and you pulled in the changes.  That's good, because it's worth touching on what it looks like the first time someone clones a repo that uses a submodule or pulls a commit that added one.

```shell
~/PART$ git pull
Updating 76b23cd..a6fb2eb
Fast-forward
 .gitmodules              | 3 +++
 Assets/Plugins/BGC_Tools | 1 +
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 Assets/Plugins/BGC_Tools
Current branch master is up to date.
~/PART$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

Looks like it worked, doesn't it?  Let's take a look...

```shell
~/PART$ cd Assets/Plugins/BGC_Tools
~/PART/Assets/Plugins/BGC_Tools$ ls
~/PART/Assets/Plugins/BGC_Tools$
```

Yep, that's saying there is not a single file in the submodule.  What happened?

It turns out, **when you clone a repository with a submodule, or pull in a commit that adds a submodule, you need to initialize it**.  You do not need to do this if you are the one who added the submodule, this was taken care of when you set it up.

```shell
~/PART$ git submodule init
Submodule 'Assets/Plugins/BGC_Tools' (https://github.com/UCRBrainGameCenter/BGC_Tools) registered for path 'Assets/Plugins/BGC_Tools'
~/PART$ git submodule update
Cloning into '~/PART/Assets/Plugins/BGC_Tools'...
done.
Submodule path 'Assets/Plugins/BGC_Tools': checked out '5ea39deadbeef8cc8c7f7eff6135ced325'
```

*Now* it's ready to use.  In summary - use `git submodule init` and `git submodule update` after you pull in a change that adds a submodule.

### Example 3: Pulling changes that update the submodule

Looks like that busybody is at it again and just updated the repo to use a newer version of the submodule he just added.  Let's see what it looks like to you if your repository was clean.

```shell
~/PART$ git pull
Updating a6fb2eb..be8c1c4
Fast-forward
 Assets/Plugins/BGC_Tools | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
Current branch master is up to date.
~/PART$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   Assets/Plugins/BGC_Tools (new commits)

no changes added to commit (use "git add" and/or "git commit -a")
```

Now git is telling us that our submodule isn't up to date with what our master branch is using.  Fixing this is just done with a simple `git submodule update`, which fetches the new submodule commits and jumps to the one we need.

```shell
~/PART$ git submodule update
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (4/4), done.
From https://github.com/UCRBrainGameCenter/BGC_Tools
   5ea390d..8205ac8  master     -> origin/master
Submodule path 'Assets/Plugins/BGC_Tools': checked out '8205ac8dfa077cf17ea6b86a5b9c304dfab7445d'
```

Now we're completely up to date.  **Remember**, `git submodule update` just updates the submodule to the commit that the base project wants it to be on (this can move the submodule forwards *or* backwards).

### Taking Stock

Let's start by looking at a few specific scenarios and what git looks like.

#### Example 4: The project and the submodule are up to date

```shell
~/PART$ git status
On branch dev
Your branch is up to date with 'origin/dev'.

nothing to commit, working tree clean

~/PART$ cd Assets/Plugins/BGC_Tools
~/PART/Assets/Plugins/BGC_Tools$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

We can see in this case that PART is up to date on the `dev` branch, and BGC_Tools is up to date on its `master` branch.

#### Example 5.1: The project and the submodule have changes

```shell
~/PART$ git status
On branch dev
Your branch is up to date with 'origin/dev'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
  (commit or discard the untracked or modified content in submodules)

        modified:   BGC_Tools (modified content)
        modified:   Assets/Scripts/TestScript.cs

~/PART$ cd Assets/Plugins/BGC_Tools
~/PART/Assets/Plugins/BGC_Tools$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   .gitignore
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

### Making Changes

If you wish to keep changes that you've made in a submodule and use them in the parent module, you *must* first commit and push the submodule changes first.  Remember, the submodule is only tracked by its commit, so you can't push and keep submodule changes that weren't committed.

#### Example 5.2: Pushing changes to a Submodule

Continuing example 5.1 above, you just need to `add`, `commit`, and `push` your changes in the submodule first...

```shell
~/PART/Assets/Plugins/BGC_Tools$ git add -A
<snip>
~/PART/Assets/Plugins/BGC_Tools$ git commit -m "Demo Changes"
<snip>
~/PART/Assets/Plugins/BGC_Tools$ git push
<snip>
```

And then it will look like this:

```shell
~/PART$ git status
On branch dev
Your branch is up to date with 'origin/dev'.

Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   Assets/Plugins/BGC_Tools (new commits)
        modified:   Assets/Scripts/TestScript.cs

~/PART$ cd Assets/Plugins/BGC_Tools
~/PART/Assets/Plugins/BGC_Tools$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

#### Example 5.3: Pushing Submodule changes to a Repo

Continuing the above example, it's now time to `add`, `commit`, and `push` in our base project.

```shell
~/PART$ git add -A
<snip>
~/PART$ git commit -m "Advancing Submodule Pointer"
<snip>
~/PART$ git push
<snip>
```

Now all of our beautiful changes have been pushed.

#### Example 6: Pulling Submodule changes into a Repo

Your ambitious coworker finally fixed all the bugs that he added with this submodule nonsense, and you need those fixes in a different project.

```shell
~/PART/Assets/Plugins/BGC_Tools$ git pull
<snip>
~/PART/Assets/Plugins/BGC_Tools$ cd ~/PART
~/PART$ git status
On branch dev
Your branch is up to date with 'origin/dev'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   Assets/Plugins/BGC_Tools (new commits)
```

Hopefully that looks familiar, because it's the same thing you would have seen if you checked the status in Example 5.3.  The next step is the same: `add`, `commit`, and `push`.

### Detatched Heads

A quick word about beheadings, because you will see them a lot with submodules.  If you're not very familiar with how to escape the state, just remember: the detatched head *means* you're not really following a branch in the traditional sense, so you want to *do that* first.  In other words, use `get checkout master` (or the branch of your choosing) to reground yourself to a branch.