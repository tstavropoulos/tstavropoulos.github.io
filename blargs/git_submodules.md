# WTF is a Git Submodule?

[Return To Blog Index](index.md)

_\<posted: 2019-04-18\>_  
_\<updated: 2019-04-19\>_

* [Introduction](#introduction)
* [Real Life Examples](#real-life-examples)
  * [Getting Started](#getting-started)
    * [Example 1.1: Adding a submodule to a repository](#example-11-adding-a-submodule-to-a-repository)
    * [Example 1.2: Adding a submodule using an existing git repository](#example-12-adding-a-submodule-using-an-existing-git-repository)
  * [Dealing with other people's changes](#dealing-with-other-peoples-changes)
    * [Example 2: Pulling in the addition of a submodule](#example-2-pulling-in-the-addition-of-a-submodule)
    * [Example 3: Pulling changes that update the submodule](#example-3-pulling-changes-that-update-the-submodule)
  * [Taking Stock](#taking-stock)
    * [Example 4: The project and the submodule are up to date](#example-4-the-project-and-the-submodule-are-up-to-date)
    * [Example 5.1: The project and the submodule have changes](#example-51-the-project-and-the-submodule-have-changes)
  * [Making Changes](#making-changes)
    * [Example 5.2: Pushing changes to a submodule](example-52-pushing-changes-to-a-submodule)
    * [Example 5.3: Pushing submodule updates to a repository](#example-53-pushing-submodule-updates-to-a-repository)
    * [Example 6: Pulling new submodule commits into a repository](#example-6-pulling-new-submodule-commits-into-a-repository)
* [Other Notes](#other-notes)
  * [Detatched Heads](#detatched-heads)
  * [Command Recap](#command-recap)
    * [`git submodule add <url>`](#git-submodule-add-url)
    * [`git submodule add <url> <subdirectory>`](#git-submodule-add-url-subdirectory)
    * [`git submodule init`](#git-submodule-init)
    * [`git submodule update`](#git-submodule-update)
* [Credits](#credits)

## [Introduction](#introduction)

If you have different projects that share any nontrivial amount of common code, especially code like generic tools and utilities, and you use git for source control you should look into a git submodule to share said common code.  Using a submodule is really just telling one git repo "keep this whole other repo here inside you, and don't manage its files".  To use an example out of game development, code representing your core game engine would live in the GameEngine repository, and all of your games built using it would have their own repositories and include GameEngine as a *submodule*.

Git submodules work in a quintessentially *git* way (I'm saying that as a good thing).  When you add a submodule inside a project, you actually just tell the project's repository to checkout a specific *commit* of the submodule repo.  So, what happens if someone `push`es new commits to the GameEngine repository and you use `git pull` in your Game repository?  Nothing happens.  *This is good.*  If submodules didn't work this way, they really would be unusable.  So if you make some breaking changes in GameEngine, you can wait to pull those changes into a project until you're ready (if ever).

## [Real Life Examples](#real-life-examples)

Below are some command-line examples showing working with Submodules.  They show how easy managing a submodule is (if you use git from command-line, which I highly recommend all programmers be comfortable with).  In the examples below, **PART** is a Unity project and git repository located in `~/PART/`, and **BGC_Tools** is a submodule from [our real, open-source repo of Unity tools](https://github.com/UCRBrainGameCenter/BGC_Tools).

### [Getting Started](#getting-started)

Let's take a look at how you can add an existing repository (`BGC_Tools`) as a submodule to a different existing repository (`PART`).

#### [Example 1.1: Adding a submodule to a repository](#example-11-adding-a-submodule-to-a-repository)

Navigate to where you want the submodule and `submodule add` it.

```shell
tstavrop:~/PART$ cd Assets/Plugins
tstavrop:~/PART/Assets/Plugins$ git submodule add https://github.com/UCRBrainGameCenter/BGC_Tools.git
    Cloning into 'BGC_Tools'...
    done.
```

Now the submodule has been added.  If we navigate back to the root of the PART project, we can see the changes.

```shell
tstavrop:~/PART/Assets/Plugins$ cd ~/PART
tstavrop:~/PART$ git status
    On branch master
    Your branch is up to date with 'origin/master'.
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   .gitmodules
            new file:   Assets/Plugins/BGC_Tools
```

So it added a `.gitmodules` file, and it represents the addition of the entire repository as a "file".  `add`, `commit`, and `push` your changes like normal.

```shell
tstavrop:~/PART$ git add -A
    <snip>
tstavrop:~/PART$ git commit -m "Adding a submodule"
    <snip>
tstavrop:~/PART$ git push
    <snip>
```

Taking a look at the `.gitmodules` file, we see it's relatively simple

```shell
[submodule "Assets/Plugins/BGC_Tools"]
        path = Assets/Plugins/BGC_Tools
        url = https://github.com/UCRBrainGameCenter/BGC_Tools.git
```

#### [Example 1.2: Adding a submodule using an existing git repository](#example-12-adding-a-submodule-using-an-existing-git-repository)

If you want to be more pedantic or precise with how a submodule is added, you have the option to `clone` the desired repo into a subdirectory first, make any desired changes, and then `add` it as a submodule.

```shell
tstavrop:~/PART$ cd Assets/Plugins
tstavrop:~/PART/Assets/Plugins$ git clone https://github.com/UCRBrainGameCenter/BGC_Tools.git
    Cloning into 'BGC_Tools'...
    done.
tstavrop:~/PART/Assets/Plugins$ git submodule add https://github.com/UCRBrainGameCenter/BGC_Tools.git BGC_Tools
    Adding existing repo at 'Assets/Plugins/BGC_Tools' to the index
```

### [Dealing with other people's changes](#dealing-with-other-peoples-changes)

Let's now take a look at how git will behave when you `clone` a repository using a submodule, or `pull` in a commit that adds a new submodule.

#### [Example 2: Pulling in the addition of a submodule](#example-2-pulling-in-the-addition-of-a-submodule)

It looks like your ambitious coworker added a submodule to the project in his last push, and you pulled in the changes.  That's good, because it's worth touching on what it looks like the first time someone `clone`s a repo that uses a submodule or `pull`s a commit that added one.

```shell
tstavrop:~/PART$ git pull
    Updating 76b23cd..a6fb2eb
    Fast-forward
      .gitmodules              | 3 +++
      Assets/Plugins/BGC_Tools | 1 +
      2 files changed, 4 insertions(+)
      create mode 100644 .gitmodules
      create mode 160000 Assets/Plugins/BGC_Tools
    Current branch master is up to date.
tstavrop:~/PART$ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    nothing to commit, working tree clean
```

Looks like it worked, doesn't it?  Let's take a look...

```shell
tstavrop:~/PART$ cd Assets/Plugins/BGC_Tools
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ ls -a
    .  ..
```

Yep, that's saying there is not a single file in the submodule.  What happened?

It turns out, **when you clone a repository with a submodule, or pull in a commit that adds a submodule, you need to initialize it**.  You do not need to do this if you are the one who added the submodule, this was taken care of when you set it up.

```shell
tstavrop:~/PART$ git submodule init
    Submodule 'Assets/Plugins/BGC_Tools' (https://github.com/UCRBrainGameCenter/BGC_Tools)
    registered for path 'Assets/Plugins/BGC_Tools'
tstavrop:~/PART$ git submodule update
    Cloning into '~/PART/Assets/Plugins/BGC_Tools'...
    done.
    Submodule path 'Assets/Plugins/BGC_Tools': checked out '5ea39deadbeef8cc8c7f7eff6135ced325'
```

*Now* it's ready to use.  In summary - use `submodule init` and `submodule update` after you pull in a change that adds a submodule.

#### [Example 3: Pulling changes that update the submodule](#example-3-pulling-changes-that-update-the-submodule)

Looks like that busybody is at it again and just updated the repo to use a newer version of the submodule he just added.  Let's see what it looks like to you if your repository was clean.

```shell
tstavrop:~/PART$ git pull
    Updating a6fb2eb..be8c1c4
    Fast-forward
      Assets/Plugins/BGC_Tools | 2 +-
      1 file changed, 1 insertion(+), 1 deletion(-)
    Current branch master is up to date.
tstavrop:~/PART$ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   Assets/Plugins/BGC_Tools (new commits)

    no changes added to commit (use "git add" and/or "git commit -a")
```

Now git is telling us that our submodule isn't up to date with what our master branch is using.  Fixing this is just done with a simple `submodule update`, which fetches the new submodule commits and jumps to the one we need.

```shell
tstavrop:~/PART$ git submodule update
    remote: Enumerating objects: 6, done.
    remote: Counting objects: 100% (6/6), done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 4 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (4/4), done.
    From https://github.com/UCRBrainGameCenter/BGC_Tools
       5ea390d..8205ac8  master     -> origin/master
    Submodule path 'Assets/Plugins/BGC_Tools': checked out '8205ac8dfa077cf17ea6deadbeef304dfab7445d'
```

Now we're completely up to date.  **Remember**, `submodule update` just updates the submodule to the commit that the base project wants it to be on (this can move the submodule forwards *or* backwards).

### [Taking Stock](#taking-stock)

Let's take a look at how git reports the `status` of a project with a submodule with and without local changes.

#### [Example 4: The project and the submodule are up to date](#example-4-the-project-and-the-submodule-are-up-to-date)

```shell
tstavrop:~/PART$ git status
    On branch dev
    Your branch is up to date with 'origin/dev'.

    nothing to commit, working tree clean

tstavrop:~/PART$ cd Assets/Plugins/BGC_Tools
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    nothing to commit, working tree clean
```

We can see in this case that PART is up to date on the `dev` branch, and BGC_Tools is up to date on its `master` branch.

#### [Example 5.1: The project and the submodule have changes](#example-51-the-project-and-the-submodule-have-changes)

```shell
tstavrop:~/PART$ git status
    On branch dev
    Your branch is up to date with 'origin/dev'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
      (commit or discard the untracked or modified content in submodules)

            modified:   BGC_Tools (modified content)
            modified:   Assets/Scripts/TestScript.cs

tstavrop:~/PART$ cd Assets/Plugins/BGC_Tools
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   .gitignore
            modified:   README.md

    no changes added to commit (use "git add" and/or "git commit -a")
```

### [Making Changes](#making-changes)

If you wish to keep changes that you've made in a submodule and use them in the parent module, you *must* first `commit` and `push` the submodule changes first.  Remember, the repository only tracks the submodule by its current commit, so you can't `push` repository changes and keep submodule changes that weren't committed, and if you don't `push` your submodule commits, then you'll be able to use them locally, but nobody else will.

#### [Example 5.2: Pushing changes to a submodule](example-52-pushing-changes-to-a-submodule)

Continuing example 5.1 above, you just need to `add`, `commit`, and `push` your changes in the submodule first...

```shell
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git add -A
    <snip>
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git commit -m "Demo Changes"
    <snip>
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git push
    <snip>
```

And then it will look like this:

```shell
tstavrop:~/PART$ git status
    On branch dev
    Your branch is up to date with 'origin/dev'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   Assets/Plugins/BGC_Tools (new commits)
            modified:   Assets/Scripts/TestScript.cs

tstavrop:~/PART$ cd Assets/Plugins/BGC_Tools
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    nothing to commit, working tree clean
```

#### [Example 5.3: Pushing submodule updates to a repository](#example-53-pushing-submodule-updates-to-a-repository)

Continuing the above example, it's now time to `add`, `commit`, and `push` in our base project.

```shell
tstavrop:~/PART$ git add -A
    <snip>
tstavrop:~/PART$ git commit -m "Advancing Submodule Pointer"
    <snip>
tstavrop:~/PART$ git push
    <snip>
```

Now all of our beautiful changes have been pushed.

#### [Example 6: Pulling new submodule commits into a repository](#example-6-pulling-new-submodule-commits-into-a-repository)

Your ambitious coworker finally fixed all the bugs that he added with this submodule nonsense, and you need those fixes in a different project.

```shell
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ git pull
    <snip>
tstavrop:~/PART/Assets/Plugins/BGC_Tools$ cd ~/PART
tstavrop:~/PART$ git status
    On branch dev
    Your branch is up to date with 'origin/dev'.

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   Assets/Plugins/BGC_Tools (new commits)
```

Hopefully that looks familiar, because it's the same thing you would have seen if you checked the status in Example 5.3.  The next step is the same: `add`, `commit`, and `push`.

## [Other Notes](#other-notes)

A few other brief notes that might be useful if you start working with submodules.

### [Detatched Heads](#detatched-heads)

A quick word about beheadings, because you will see them a lot with submodules.  If you're not very familiar with how to escape the state, just remember: the detatched head *means* you're not really following a branch in the traditional sense, so you want to *do that* first.  In other words, use `checkout master` (or the branch of your choosing) to reground yourself to a branch.

### [Command Recap](#command-recap)

Here is a quick recap of all the git commands that were used here:

#### [`git submodule add <url>`](#git-submodule-add-url)

* Clones out the git repository from `<url>` (as if you had called `clone <url>`) and tracks it as a submodule
* `<url>` can be a local file path, or a web url

#### [`git submodule add <url> <subdirectory>`](#git-submodule-add-url-subdirectory)

* Same as [`git submodule add <url>`](#git-submodule-add-url), but you can specify where the submodule is to live
* If `<subdirectory>` already exists, then it *must* already be a git repository.  If you wanted to `clone`, then you must delete the directory first

#### [`git submodule init`](#git-submodule-init)

* Does initial setup on submodules that have been pulled or cloned
* You *do not* need to call this if you added the submodule through `submodule add`
* You *do* need to call this if you just performed `pull` or `clone` and a new entire submodule has been added in the commits you integrate

#### [`git submodule update`](#git-submodule-update)

* Jumps the submodule directly to the commit that the repository expects it to be on, performing a `fetch` for the submodule if necessary.
* *Does not* advance the submodule *past* the last commit that was checked in
  * To rephrase, if you `push` a new commit directly to the submodule's repo, performing `submodule update` on a repository that uses it *does not* pull in these new changes
  * To advance the submodule commit, you need to `pull` (or `checkout <branch>` and then `pull`) within the submodule, and then `add`, `commit`, and `push` from the main repository
* In essence, the only feature of the submodule that the repository sees or cares about is what commit it is on.

___

## [Credits](#credits)

The contents of this blog are open source and pull request are welcome.  If anything I said is wrong, or you see some way that this could be more clear, send me a pull request or shoot me an email (Trevor.Stavropoulos@gmail.com) and I'll integrate your changes and give you credit.

Special Thanks To:

* [Me](https://tstavropoulos.github.io), naturally
* [Colan Biemer](https://bi3mer.github.io/) for forcing our first submodule upon me
