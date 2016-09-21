# Git Tutorial for Dummies

This is meant as a very simple tutorial for collaborating on a paper using git/github. I will provide some background and explanation of the structure of git, but the main take-away is that you will only need 5 commands to run git `clone, add, commit, pull` and `push` and optionally `status`.
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

## Basic Concept (Optional)
The repository keeps track of three trees/indexes, the `Working Directory`, `Index` and `HEAD` which will be explained below. This part may be useful to understand the concepts, but is not nessasary to understand in order to use git.
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
The question now is how this is supposed to work when several people are working on changes to the project at the same time. The following workflow is recommended.
Before making any changes, always run:
```bash
git pull
```
This will make sure any changes on github will be pulled to your local repository. Unless you made any changes before pulling, this should not create any conflics, it it does, follow the instructions for conflics below.
Then make your changes and run
```bash
git add file1.tex file2.tex
```
for the files you have made changes to. The pdf file and other generated files should not be uploaded, they will only generate conflicts.
Then `commit` your changes locally and add some message explaining what you did
```bash
git commit -m "Updated introduction"
```
You may do several changes (`add` and `commit`) locally before bushing to the server, but this increases the risk of conflics.
When you are satisfied with your local changes, push them to the server
```bash
git push
```
you will be ased for your ssh password. If there were no changes on the server since you did your last `pull`, you will see a message containing `Writing objects:` and all your changes (that you added) will be uploaded. If there were 
