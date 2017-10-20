# git cheatsheet #

<br />
## git config ##
- set the basics
	
		git config --global user.name "Jon Loeliger"
		git config --global user.email jdl@example.com
		git config --global core.editor emacs

- show the configurations
		
		git config -l

- unset a parameter
		
		git config --unset --global user.email

- edit the .gitconfig file

		git config --global --edit

- remember credentials
>remember the credentials for 5 minutes, now you push/pull without typing name/password

		git config credential.helper 'cache --timeout=300'

<br />
##Usage##

- get the help for the commit

		git help commit

- make empty repository. Simply run git init in the directory which contains the files you wish to track for public, no worries you wont see unnecessary git folders
>execute this on the server only
		
		git init --bare 

- add all files to the new repo
>execute this on the host

		git add .

- add commit message to track what you did.
>execute this on the host, will commit all the added files

		git commit -m "Initial contents of public_html" --author="john milis <milis@mail.com>"

- If you want to get a copy of an existing Git repository — for example, a project you’d like to contribute to
>execute this on the host
		
		git clone git://github.com/schacon/grit.git

- determine which files are in which state
>shows newly created files, untracked files

		git status

- To see which remote servers you have configured.
>execute this on the host
		
		git remote 

- specify -v, which shows you the URL
>execute this on the host

		git remote -v

- add a new remote Git repository as a shortname you can reference easily
>execute this on the host

		git remote add [shortname] [url]

- commit and add at the same time
>no need to execute git add file <br />
>useful when you want to fix a minor detail you forgot on the files you were already working on

		git commit -am "fixed this and that on previous file"
		git push origin branch-name

<br />
##branching##

- add branch

		git branch name-of-branch

- list branches, and on which branch we are on
		
		git branch

- switch to branch
		
		git checkout another-branch

- push to branch
>-u: glue this local branch to the upstream <br />
>so -u is for new branch only, and for first push only <br />
>on next pushes no need to add -u, just add the branch-name 

		git push -u origin branch-name

##Commit history ##
- [book ref](http://git-scm.com/book/en/Git-Basics-Viewing-the-Commit-History)
- Just show the commit history messages.
>git log

##Git Pull##
Will pull down from a remote whatever you ask (so, whatever trunk you’re asking for) and instantly merge it into the branch you’re in when you make the request.
	
	git checkout localBranch
	git pull origin master
	git branch
	master
	* localBranch

##Git fetch##
Fetch is similar to pull, except it won’t do any merging.

	>git checkout localBranch
	>git fetch origin remoteBranch
	>git branch
	>master
	>* localBranch
	>remoteBranch

##Git clone##
Git clone will clone a repo int a newly created directory. It’s useful for when you’re setting up your local doodah

	cd newfolder
	git clone git@github.com:whatever/something.git
	git branch
	* master
	remoteBranch

<br />
##Update your repository's master branch on the server##
	git fetch other-repository
	git checkout master
	git branch -a
	git merge other-repository
	git status
	git push your-repository master
This will merge other-repository master with the branch you are on. <br />
Alternatively you can pull and then push.

<br />
About
=====

Author
--------------
- Original author: Giannis Petrousov
