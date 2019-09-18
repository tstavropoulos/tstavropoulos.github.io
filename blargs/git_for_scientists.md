# Git (For Scientists)

[Return To Blog Index](index.md)

_\<posted: 2019-09-17\>_  
_\<updated: 2019-09-18\>_

* [Introduction](#introduction)
* [What is a Version-Control System?](#what-is-a-version-control-system)
* [But... what should I use Git for?](#but-what-should-i-use-git-for)
  * [What types of files should I use with Git?](#what-types-of-files-should-I-use-with-git)
  * [How is this different from a backup?](#how-is-this-different-from-a-backup)
  * [Sounds Great. How do I Git-up my whole computer?](#sounds-great-how-do-i-git-up-my-whole-computer)
  * [What is the difference between Git and GitHub?](#what-is-the-difference-between-git-and-github)
  * [Fine. How do I get started?](#fine-how-do-i-get-started)
* [Git Terminology](#git-terminology)
  * [Repository](#repository)
  * [Commit](#commit)
  * [Branch](#branch)
  * [Merge](#merge)
  * [Clone](#clone)
  * [Fork](#fork)
  * [Push](#push)
  * [Pull](#pull)
  * [Rebase](#rebase)
  * [Add](#add)
  * [Pull Request](#pull-request)
  * [.gitignore](#.gitignore)
* [Using Git - An Example](#using-git---an-example)
* [Resources](#resources)
* [Afterword](#afterword)
* [Credits](#credits)

## [Introduction](#introduction)

As a programmer working with scientists, I am asked with some regularity, "What is Git, and why would I use it?" Far more frequently, however, I find myself asking others, "Why *aren't* you using Git (or some other [Version-Control System](https://en.wikipedia.org/wiki/Version_control))?". It is my hope that this becomes a page I can send someone when the inevitable reply arrives, "What is Git?".

Teaching users *how* to use Git is beyond the scope of this post. It is my hope that this can serve as a good explanation of the features of and motivation for Git, which should provide the necessary context to make digesting other tutorials on Git easier and faster.

Since the beginning of Time (January 1st, 1970 00:00 GMT), programmers have had to deal with managing a lot of files, many of which would undergo numerous iterative changes. To make matters worse, many of these programmers would have the audacity to simultaneously work together on projects, making it necessary for them to track their respective changes and combine them together into the developing project. This is a lot of work, and very important to get right, so the first Version Control systems were developed by programmers, for programmers.

## [What is a Version-Control System?](#what-is-a-version-control-system)

Since this article isn't really about the history of VCS, or even about any VCS other than Git, I'm going to skip straight to what Git does and why we use it. In the broadest possible sense, Git is a sophisticated way of tracking changes to files. If you have ever used _**Track Changes**_ in a Word document, then you're familiar with the concept of receiving a batch of changes from a collaborator, and being able to inspect the document *before* and *after* the new changes are applied. If you haven't... well, it does exactly what I just described.

Git takes this concept and runs with it, but for a whole directory of files instead of just a single one. Sets of changes are bundled together into a _**commit**_. A _**commit**_ might have changes like "Change *this* sentence to *that*" and "Create a new file called *this* with *that* text", etc. A _**commit**_ can be large or small, but ideally they are a somewhat cohesive sets of changes - think "Adding a new simulation type" instead of "All April and May 2019 changes".

Tracking changes in this way unlocks another very important feature of Git, virtually for free - it's trivial to revisit old versions. It becomes easy to answer very common questions like "what changed since the last version?" and "Did this error exist 2 versions ago, or is it new?"

Git's usage also makes collaborating with others relative easy. You can integrate a commit from someone else just as easily as you integrate one of your own, and it comes with many tools for handling *conflicts* - ie, determining what should happen when both you and a collaborator just changed the same file in mutually incompatible ways.

## [But... what should I use Git for?](#but-what-should-i-use-git-for)

I can hear a number of you through the computer saying "Great, Git helps me track, merge, share, and roll back changes. But what should I actually use it for? If I'm working by myself, isn't that just the same as a backup?"

I'll try to tackle the natural questions in a useful order.

### [What types of files should I use with Git?](#what-types-of-files-should-I-use-with-git)

Broadly speaking, Git is ideal for projects which contain text files that are being modified or improved over time. Git can work on binary files (like images), but its strengths lie in tracking changes to text files. Viewing the history of edits of a raw text file (like MatLab code, a paper draft written in LaTeX, a simple text file, etc) will give you a clear picture of how that text file has been changed. But changes to binary files like images, PDFs, or even Microsoft Word documents (though there are [workarounds](https://github.com/vigente/gerardus/wiki/Integrate-git-diffs-with-word-docx-files) for this) are going to be inscrutable without jumping back to the old version and opening it up to take a look. You can still do this, of course, it's just marginally less helpful than being able to see how it has changed.

### [How is this different from a backup?](#how-is-this-different-from-a-backup)

This is a common comparison, but it is not quite right - at least, it's not complete. You could definitely use Git to track the history of a file, allowing you to recover old versions. But if the hard drive dies, both your current and old versions are gone unless you backed them up elsewhere. An incremental backup system, like Apple's TimeMachine, lets you recover old versions of files, but with far less clarity and control. And using Git with an incremental backup is indeed redundant. A backup is for *recovery*, so you don't lose your files in the case of a disaster, whereas Git is for tracking and sharing changes. However Git does make it very easy to use an external Git server/service, like GitHub, to create a proper backup of your work (security experts will quite reasonably caution you about exclusively relying on a 3rd party for backups, but that's beyond the scope).

### [Sounds Great. How do I Git-up my whole computer?](#sounds-great-how-do-i-git-up-my-whole-computer)

Not so fast... The best way to use Git is to track individual projects, separately. A Git "project" is called a _**Repository**_ (sometimes *repo* for short). Indeed, you *could* put all of your work in one folder, make that folder a Git *repo*, and call it a day. But this would fail to capitalize on some of the strengths of Git. Instead, if you made a single Git repository for a single MatLab simulation you have, or a paper you're working on, then all of the changes in the history are relevant to that project, and it's significantly easier to Share, Examine old versions, and Fork (if you want to make some incompatible changes and take the project in a mutually-exclusive direction).

### [What is the difference between Git and GitHub?](#what-is-the-difference-between-git-and-github)

Git is the program (initially created by Linus Torvalds in 2005 when he was working on the Linux Kernel) that does all this work in tracking, handling, and sharing changes. GitHub is the Website/service that (among other things) allows you to backup, share, and manage some Git repositories on the internet. You *can* have private Git repositories on GitHub that only you (or you and a few people you select) can access, but it should go without saying that you should be careful about uploading sensitive files to the internet. You can use Git without GitHub, but there is no GitHub without Git.

### [Fine. How do I get started?](#fine-how-do-i-get-started)

If you're already comfortable using a command-line interface (that is, typing into a terminal or command prompt), then getting started is incredibly easy and there are many tutorials out there that will walk you though it. For everyone else, there are a lot of tools designed to make the process simpler. I tend to recommend [GitHub Desktop](https://desktop.github.com/) to new users, as it makes some of the common features relatively easy to use.

## [Git Terminology](#git-terminology)

A quick reference on some common terms used when discussing Git.

### [Repository](#repository)

A single Git project. It tracks the history of all [commits](#commit), and is completely decentralized. This means that every copy of the repository has a complete view of the history, not just a snapshot of the current state.

### [Commit](#commit)

A single bundle of changes within a [Repository](#repository). A commit contains all of the changes, the author who created the commit, the date it was created, and usually a Title and Description of what the commit contains. The History of a repository is all of the Commits that brought it from just an empty directory to the state it's in now. Making changes to files within a repository doesn't automatically create a Commit - you do that deliberately when you're ready by `Add`ing and `Commit`ing the files you wish to be included, and providing the description.

### [Branch](#branch)

A branch in Git is like a branch in the road of a [Repository](#repository)'s history (indeed, the term wasn't chosen arbitrarily). The main, primary branch in a repository is usually called the `master` branch. Its history is just a linear sequence of changes to create the current version. However, if you went back to an old version of the repository and make different changes, you could save that as a new `branch`, and freely switch between them. Frequently in shared programming projects, the `master` branch is where the current stable code lives that outsiders should use, and new features that aren't yet complete are be developed in other branches, and then [merged](#merge) into the `master` branch when they're ready.

### [Merge](#merge)

Merging is where the changes in two different [branches](#branch) are reconciled and one branch is updated to include them all. If I committed some changes that tweaked some of the math of a simulation, and you created a commit with some changes to make it faster and more efficient, then the process of me updating my repository with your changes would be merging.

### [Clone](#clone)

The process of downloading a repository, usually (but not always) running on a server (like GitHub). A cloned repository is generally "meant to" stay up-to-date with its source.

### [Fork](#fork)

The process of spinning-off a new repository that's a copy of some point in the history of an existing repository (called the "upstream" repository). Frequently repositories are forked so that the Forker can have control over changes. (See [Pull Request](#pull-request))

### [Push](#push)

The process of uploading your new commits to another copy of the repository, usually (but not always) running on a server (like GitHub).

### [Pull](#pull)

The process of downloading new commits from another copy of the repository, usually (but not always) running on a server (like GitHub).

### [Add](#add)

Not every change that exists in a repository must be included in the next commit. Before creating a commit, each of the modified files that you'd like the commit to actually change are added to a list of the pending changes. If you changed 2 files and deleted a third, you could choose to "add" the changes, but not the deletion, to the next commit. This would work whether you just wanted to include the deletion as part of a separate commit, or even if you didn't wish to include it at all.

### [Rebase](#rebase)

A bit complicated to appropriately cover here, but essentially it is the process of updating commits as if they came later. If Commit A modified the first line of "TestFileA.txt", and Commit B renamed the file to "TestFileB.txt", then if you Rebase Commit A onto Commit B, then Commit A would say that it modified the first line of "TestFileB.txt".

### [Pull Request](#pull-request)

A GitHub-specific concept, this is a recommendation that specific changes be made to a repository. For example, if you identified a bug in a public repository you were using, you could [fork](#fork) the repository, create a [commit](#commit) that fixes the bug, and then open a pull request for the original repository with the commit and an explanation of the issue. If the owner accepts the pull request, then your changes would be [merged](#merge). This is how public code and documents can be improved without devolving into [narcissistic anarchy](https://en.wikipedia.org/wiki/Ayn_Rand).

### [.gitignore](#.gitignore)

A special file that you can add into a Git repostory, always with the name ".gitignore". Unsurpisingly this file tells Git what files to ignore. Git will act like any file or directory described in it doesn't exist, whether described literally (`Examples/DataFile.txt`) or with a wild card pattern (all csv files with `*.csv`). This is useful to avoid, for example, adding temporary autosave files like MatLab's "scriptName.m~" (with the pattern `*.m~`), or compiled output (sometimes `*/obj`).

## [Using Git - An Example](#using-git---an-example)

This section is to explain how the process of using Git might look, but is not by any means a proper tutorial on its usage. For convenience and reference, proper git commands will be highlighted.

To start with, an existing project directory is `init`ialized with Git, optionally a relevant `.gitignore` file is created, all the "modified" files (literally everything in this case) are `add`ed, and then the changes are `commit`ed.

More changes are made to the project files. When complete, they are `add`ed to form the list of pending changes and those changes are `commit`ed.

At a later point, it becomes necessary to share the project with a collaborator, or perhaps it's just necessary to do some work on a different computer. A new repository is created on GitHub. In the existing repository, the `remote` is configured, and then all of the commits so far are `push`ed.

On this secondary machine, the GitHub repository is `clone`d. Then some of the files are modified, `add`ed, and `commit`ed. Finally, this new commit is `push`ed to GitHub.

Now, the original project can `pull` the new commits from GitHub, adding the new commit to the original computer.

If the changes were bad, the `log` could be inspected, a desired commit selected, and then one could `checkout` said commit, resetting the repository to an earlier state. One could even just `revert` the bad changes, which would automatically create new commits that undo the actions of the undesired commits (not merely purge them from the history).

## [Resources](#resources)

When it's time to take the plunge and learn how to use Git, here are some learning resources I can recommend:

* [GitHub Learning Lab](https://lab.github.com/) - The first few tutorials here are a good way to get started
* [Katacoda Git Tutorials](https://www.katacoda.com/courses/git) - Interactive git tutorial in the browser, but expects some knowledge about basic shell commands. (Since this actually reveals how you solve each step with the push of a button, you could work around having no command line experience)
* [Resources to learn Git](https://try.github.io/) - The resources on this page are all pretty useful, Learn Git Branching being no exception.
* [A quick Git CLI primer](https://medium.com/@george.seif94/a-full-tutorial-on-how-to-use-github-88466bac7d42) - A brief primer on using GitHub on the Command-Line Interface.

## [Afterword](#afterword)

Thanks for taking the time to read this. I will try to expand it as more useful details occur to me. If this was useful to you, or you feel I missed some important details, please let me know.

___

## [Credits](#credits)

The contents of this blog are open source and pull request are welcome. If anything I said is wrong, or you see some way that this could be more clear, send me a pull request or shoot me an email (Trevor.Stavropoulos@gmail.com) and I'll integrate your changes and give you credit.

Special Thanks To:

* [Me](https://tstavropoulos.github.io), naturally
* [Randy Mester](https://github.com/randymester) for help with proofreading and content
