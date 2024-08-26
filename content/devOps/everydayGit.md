---
title: "Everyday Git Workflow"
date: 2022-12-15
slug: "everydayGit"
description: "Everyday Git Workflow"
keywords: ["github", "git", "gitlab"]
draft: false
#tags: ["docker", "container"]
math: false
toc: true
---

The origin story of Git is an epic tale of self-reliance - Linus Torvalds, utterly frustrated with the version control software that was available at the time, built Git in a ridiculously short time and it has become a ubiquitous industry standard worldwide. Sometimes the [back story](https://www.linuxjournal.com/content/git-origin-story) is just as much fun as the software itself!

Git is a fundamental tool in the development toolbox; every Software Developer or DevOps Engineer should be familiar and comfortable with it.  While there are much deeper guides available, the basic steps needed to get up and running (and productive) with Git in a new project/company are discussed below.

## The Initial Setup

Obviously, making sure that your computer (lets say a new laptop at a new firm) has Git installed is a good first step.  If you are fortunate enough to have been supplied a Linux laptop, it is probably already installed and ready to roll.  

If Git is not installed, a simple `sudo apt install git` or `sudo dnf install git` command (or whatever other flavour of package manager your particular distribution uses) will get `git` neatly installed and ready to go.  On Mac or Windows, you'll need to install Git from the [Git Downloads](https://www.git-scm.com/downloads).  Whether Linux, Mac, or Windows, this tutorial will be focused on the Command-Line-Interface (CLI or terminal) version of Git, so the steps in this quick guide are the same regardless of Operating System.

### Basic Configuration

Once Git is installed, the next step is to tell it a little about you, the user - particularly your name and email address.  This is **crucial** in tracking commits - the who/when trail is essential.  In the CLI (whether Git Bash or Linux terminal), enter the following commands:

``` shell
git config --global user.name "Brad Penney"
git config --global user.email "nearlyGotMe@niceTry.com"
```

Another **optional** configuration I really like to set is the default editor:

``` shell
git config --global core.editor vim
```
This way, Git won't default to `nano` and have your new colleagues worrying about the noob's skill-set.  Don't hesitate to check out [First-Time Git Setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) for a much more detailed summary of available setup and configuration options. 

## Cloning Remotes

If you're joining an ongoing project, you'll need to `clone` an existing repository.  If its an entirely new project, its still a good idea to start by logging into the remote repository of choice ([Gitlab!](https://about.gitlab.com/)), create a new repository, and then `clone` it to the local machine.  In either case, the same command gets the job done:

``` shell
git clone git@gitlab.com:Brad_Penney/prog1700_fall_2022.git # Example (of course)
```

Be sure to `clone` in a suitable parent directory - the above command will create its own directory with the same name as the remote repository (i.e. `prog1700_fall_2022`).  The huge advantage of creating a remote repository and cloning it is that several commands are skipped, such as setting the `origin`, which can be confusing and error-prone for beginners.  

## Branching Out

Now that the repository has been cloned to your local machine, all the source code for the project is present.  Obviously, the code itself will vary based on the project's maturity level.  The next step is to create your own branch to begin work on a feature or hotfix.  Sometimes this naming convention will be already decided - "Feature23A" or "HotFix156B" - and sometimes you get to choose the name of your branch.  Either way, creating it is the same command:

``` shell
git checkout -b Brad # Would create a new branch called "Brad"
```

Congratulations, you now have your very own branch - a copy of the source code that you can beautify, nurture, and call your own.  Seriously though, this is the beauty of Git - you've now got your own sandbox that you can build/test features and hotfixes in without fear of ruining the source code of the entire project if you mess things up.

Once you've made some meaningful changes, you'll want to commit them and push them up to the remote repository with these commands:

``` shell
git add . # Adds all files.  Accepts standard globbing, so pick and choose your files as needed
git status # Lists the files that will be committed
git commit -m "Always add a meaningful message! Seriously!" # Commit the changes
git push origin <branchName> # BE SURE YOU ONLY PUSH YOUR BRANCH!!
```
You've now added your changes to the remote repository.  A senior developer (often the lead) will perform a code review and possibly a `pull request` to integrate your code into the `main` or `master` branch.

## The Daily Merge

At this point, you've got your own code sandbox, and have added some changes to the remote repository - you're contributing to the team already! But... what about all the changes that the rest of the team applies to the `main`/`master` branch while you toil away on your feature or hotfix? Shouldn't you be working with the latest and greatest codebase?  

The answer is simple thanks to the magic of Git - perform a `merge` with the `main` or `master` branch periodically (once per day *usually* makes sense).  To merge the codebase changes with your own `branch`, follow this procedure in your CLI:

``` shell
git checkout master
git pull
git checkout <yourBranch>
git merge master
```
With any luck, you won't have any `merge conflicts` as your lead software developer has assigned tasks in such a way that everyone is working in their own little area and not stepping on each other's toes.  If `merge conflicts` are encountered, consult a senior developer to resolve them (but make sure your editor is pointed at `vim` and not `nano`!)

## In Conclusion

You're now ready to use Git to show off your skills and be a productive, collaborating member of a Software Development or DevOps team.  Obviously this very quick introduction only scratches the surface of the powerful version control system that is Git, however, it gives you the tools you need to get going.

If you're interested in becoming a Git aficionado, I suggest starting with [Pro Git](https://www.git-scm.com/book/en/v2) - the documentation from the official website.  For Git's origin story, [check out the legend here](https://www.linuxjournal.com/content/git-origin-story).