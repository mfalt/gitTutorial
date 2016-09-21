# Git Tutorial

This is meant as a very simple tutorial for collaborating on a paper using git/github. I will provide some background and explanation of the structure of git, but the main take-away is that you will only need 5 commands to run git: `clone, add, commit, pull, push` and optionally `status`.
## Background
I will provide a private git `repository` on my Github (https://github.com/mfalt/foundationsandtrends.git) in which we can collaborate on the paper. This repository can be `cloned` to your local computer, where you can work on the project. When you are satisfied with your changes, these can be `pushed` to the central repository, more on this later.
Git runs locally on your computer and is used to keep track of the files and history of the project and your changes. The files are contained in a git repository which is a simple folder on your computer. 

## Getting the repository (Only done once per computer)
Since the repository on GitHub will be private (not anyone can read and write) you will need a GitHub account, and I will have to provide you with access. To read and write you will have to either provide your github username and password to git or use an `ssh` key, I will explain the latter here. These commands should work on Mac and Linux, and some of them might require administrator rights (`sudo`)
```bash
# First verify that you don't already have a key by listing the files in the .ssh directory
# If you do, go to "Add key to ssh agent"
ls -al ~/.ssh
```
Create your key
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# You should see:
# Generating public/private rsa key pair.
```
When asked to "Enter a file in which to save the key", press Enter. And then type a password for the ssh key twice when asked.
### Add key to ssh agent
Make sure the ssh-agent is running
```bash
eval "$(ssh-agent -s)"
# You should see something like:
# Agent pid 59566
```
then add the key to the ssh-agent
```bash
ssh-add ~/.ssh/id_rsa
```
### Inform github about your key
Copy the all the text in `/.ssh/id_rsa.pub`, this is your "key". Go to Github and click your profile photo in the upper right corner. Press "Settings". In the left bar press "SSH and GPG keys". Click "New SSH key or Add SSH key." Add a title and paste your key into the "Key" field. Press "Add SSH key" and enter your password if prompted. This key can now authenticate you with github!

## Download the repository (`clone`)
One I have given you access to our private repository, you can run
```bash
git clone git@github.com:mfalt/foundationsandtrends.git
```
this will create the repository (folder) `foundationsandtrends` in your current directory. You will be asked for your ssh password at this time.
If you are authenticating with username and password instead of ssh key, run the following command instead
```bash
git clone https://github.com/mfalt/foundationsandtrends.git
```

## Basic Concept (**Optional**)
This part may be useful to understand the concepts, but is not nessasary to understand in order to use git.
The repository keeps track of three trees/indexes, the `Working Directory`, `Index` and `HEAD` which will be explained below. 
You can always check the status of the repository by writing
```bash
git status
```
in the terminal. If no changes are made you will see, among other things:
```bash
nothing to commit, working directory clean
```
The `Working Directory` is simply the folder you are working in, and any changes you make will be reflected here directly. If you create a new file, `git status` will provide a message like
```bash
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	test3.tex
```
This means that you have edited the file locally, but it is not tracked by git, i.e. it is not in the git `Index`. To add the file to be tracked, write `git add test3.tex`. `git status` will now return, among other things
```bash
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   test3.tex
```
This means that git will keep track of the changes in this file, and when you are satisfied with them, you can run
```bash
git commit -m "My explanation of what I did"
```
to add them to the history in the git `HEAD`. The same `add` and `commit` procedure is done when editing an existing file. Serveral changes in different files can be saved in a single `commit`.

## How to work with git: `pull`, `add`, `commit`, `push`
The question now is how this is supposed to work when several people are working on changes to the project at the same time. I recommend the following workflow, and most of the time you will only have to do the following:
```bash
git pull
# make changes
git add myfile1.tex
git commit -m "Some text"
git push
```
but more work is sometimes needed, an explanation follows.
Before making any changes, always run:
```bash
git pull
```
This will make sure any changes on github will be pulled to your local repository. Unless you made any changes before pulling, this should not create any conflics, it it does, follow the instructions for conflics below.
Then make your changes and run
```bash
git add file1.tex file2.tex
```
for the files you have made changes to. **The pdf file and other generated files should not be uploaded**, they will only generate conflicts.
Then `commit` your changes locally and add some message explaining what you did
```bash
git commit -m "Updated introduction"
```
You may do several changes (`add` and `commit`) locally before pushing to the server, but this increases the risk of conflics.
When you are satisfied with your local changes, push them to the server
```bash
git push
```
you will be ased for your ssh password. If there were no changes on the server since you did your last `pull`, you will see a message containing `Writing objects:` and all your changes (that you added) will be uploaded. If there were any updates on the server, you will se a message containing
```bash
 ! [rejected]        master -> master (fetch first)
```
and you will have to incorporate these changes locally before being able to push everything to the server.
To get the recent changes, just run
```
git pull
```
and if there were no conflics, git will merge the updates automatically and you create a new commit for you.
You will see an edito rwith the commit message "Merge branch 'master'", which you can edit the if you want. When you have closed the editor you can then run
```bash
git push
```
to send the merged changes to the server.
If git was not able to merge the files automatically, for example if changes were made on the same row both locally and on the server, you will instead get a message containing
```bash
CONFLICT (content): Merge conflict in somefile.tex
```
You will now have to decide manually on how the conflict should be fixed.
## Dealing with a conflict
Open the file with the conflict and you will see something like
```tex
\begin{document}
<<<<<<< HEAD
\section{Algorithm}
=======
\section{Introduction}
>>>>>>> 43951145ce03162f4ead57ce460a37a56df659d3
\end{document}
```
The part after `<<<<<<< HEAD` are the changes that you made locally, and the part after `=======` were made on the server. In this case, both changes should be kept, so edit the file to how you want it to look:
```tex
\begin{document}
\section{Introduction}
\section{Algorithm}
\end{document}
```
Then tell git that you have fixed the conflict in this file and create a commit telling what you did
```shell
git add somefile.tex
git commit -m "Merged the changes in somefile.tex"
```
Now push the changes to the server
```shell
git push
```

## General tips
* Keep the different sections in different files, this reduces the risk of conflicts.
* Keep the lines in the file as short as possible (for example a new line after every comma and period). This makes it possible for people to edit the same paragraph without creating a conflict on the same line.
* Push and pull regularly, to avoid large conflics.
