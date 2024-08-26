---
title: "Why VIM?"
date: 2023-05-24T19:22:49-03:00
draft: false
toc: true 
---

In a world where text editors and IDEs abound, using a terminal editor like VIM may seem an odd choice - even to some developers.  Offerings like Visual Studio Code, IntelliJ, Eclipse, Atom, Sublime Text, and Notepad++ build a strong case that terminal editors are antiques that should be retired to the trash bin of history.  However, I believe there is a very strong case that learning VIM is well worth the invested time, and will pay back dividends over your career.  

There are three compelling reasons to learn VIM thoroughly.  VIM is:

1. Available nearly everywhere
2. A modal editor
3. Easily customizable

![VIM](/devops/vimLogo.png)

## Available Nearly Everywhere

In enterprise computing, not every IDE and text editor will be approved for use by your company.  If, for example, you move to a company that doesn't allow Visual Studio, and you've spent hundreds of hours in that (powerful) IDE in a former position, the transition might be a little on the rocky side.

Even more important - the IDEs and text editors listed above are rarely available on servers (which are usually ~95% Linux with only the command-line).  So, if some configuration file needs fixing on a server (or a hotfix is done to the source code), using an IDE or GUI-based text editor simply isn't possible. Fumbling around an unfamiliar text editor while screen-sharing during a critical incident is not a good time.  Trust me.

VIM, however, is available *basically* everywhere.  On Windows, its included in the ubiquitous GitBash program - which is in use in nearly every enterprise computing scenario.  Its also installed by default on nearly every Linux distribution imaginable - even UNIX clones like AIX have `vi` - any competent VIM user would be perfectly comfortable in `vi`.  VIM is even available inside some containers - BusyBox, for example, contains `vi`.  So if you get comfortable (and good) in VIM, you'll be able to use these skills nearly everywhere - not just on a very specific system.

## A Modal Editor

Unlike most IDEs and text editors, VIM is modal - it has two primary modes, and keys do different things in different modes.  This is kind of like having super-powers - a simple `gg` takes you to the end of a document, or `^` takes you to the start of a line, or `13yy` copies (yanks) 13 lines downwards, starting with the one the cursor is on. Anyone who gets familiar with both modes in VIM will be able to jump all over a document, copy(yank)/paste text, insert text from other documents, modify multiple files at once in split windows, and much more. All without ever lifting their hands from the home row - using the mouse (or even arrow keys) is unnecessary.

This is both a blessing and curse for VIM - for beginners the unfamiliarity of a modal editor makes the learning curve steep.  Those who've spent time summiting that curve scoff at the inefficiency of grabbing a mouse to do everything. VIM is beginner-friendly, offering the `vimtutor` program to get started.  I recommend beginners go through `vimtutor` a few times, preferably once-a-day for a week (or so) to internalize VIM's commands and flatten that learning curve.

At this point I should confess that I'm a bit biased towards keyboard-only computing. It really does make me significantly more productive to use keyboard-shortcuts for nearly everything, whether in Windows or Linux.  I encourage all users to start learning keyboard-shortcuts - but try to stick to shortcuts that are available in a vanilla system (don't become dependent on custom key bindings!).

## Easily Customizable

Customization is a tricky subject.  I do not suggest that people customize their systems significantly, but sometimes a little light customization can be helpful.  The argument against extreme customization ("making it your own") is simple - in enterprise, you'll touch hundreds if not thousands of systems.  If you become dependent on unique customizations, this reduces your ability to jump to a new system quickly and be productive immediately.

With that in mind, VIM is easily customizable without being extreme.  I would strongly advise only adding minor customizations that make you more productive, are **very easily** configured, and are close enough to how VIM normally works to be familiar without them.  

To customize VIM, simply create a `.vimrc` file in the user's home directory.  If you're using GitBash, this is wherever the `.exe` file is stored.  If you're on any \*nix flavour, the home directory is usually `/home/<username>` (depending on your enterprise setup of course).  Below is an example of a very simple `.vimrc` file that makes me more productive, is easy to retype (if pulling from a personal repo isn't available), and I can easily go without it if needed.

``` vim
" Set numbers
set nu
set rnu

" Remap jj to Escape
inoremap jj <Esc>

" Turn on search highlighting
set hlsearch
set incsearch

" Scroll Distance
set scrolloff=3
```

This `.vimrc` file performs some light customizations of things I prefer - things like relative line numbering, remapping of `jj` to `<Esc>` (to save my poor pinky), and setting scrolling so that the screen moves before I get to the extreme bottom or top of a document.  If I am confronted with vanilla VIM, I'm still comfortable.  If in the course of my work I have to use a system constantly, I'll take the 2 minutes needed to add my `.vimrc` and feel nice and cozy.

## The Case for VIM

While many developers have strong opinions on what they like to use to write code, from a practical standpoint it makes a lot of sense to get good at VIM.  It's my daily-driver for Python, BASH, YAML, and Markdown.   Because I've invested in learning the modal nature of VIM, I am faster and more efficient than if I were using a mouse in an IDE.  Best of all, if I end up screen sharing while using VIM, I am thinking about the changes being made, not about how to get into INSERT mode, or how to jump to the next line. No more (or at least less) fumbling like a noob in front of my colleagues.  There will always be *some* fumbling while I'm "driving"... ;)

Am I an expert?  Nope.  There is always something to learn in VIM.  It is versatile, powerful, ubiquitous, and a genuine pleasure to use after you get comfortable - which is really all you can ask of your tools. 
