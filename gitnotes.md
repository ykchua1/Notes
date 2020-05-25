Use this to see the contents of the git object
	git cat-file -p [hash] (-p is for pretty print option)
	git show [hash]

A git object is a blob | tree | commit
	all objects are content-addressed by their hash (SHA1)

We can have human readable names like 'master'
	a special reference name is HEAD, meaning "where we currently are"

2 uses of git checkout:
	1. checkout branches - git checkout <branchname>
	2. discard changes to file - git checkout -- <file>

useful note: all git commands map to some manipulation of the commit DAG (graph)
by adding objects and adding/updating references

the purpose of the git staging area is to allow user to choose what modifications to commit

to clone from github repos more quickly:
	git clone --shallow: clone without the entire version history

to see who edited each line:
	git blame <file>

git fetching and merging
	git fetch: fetch from remote (retrieve objects and references from a remote)
	git merge: merge into current branch
	git pull: same as git fetch; git merge

git remotes
	git remote: list remotes
	git remote add <name> <url>: add a remote
	git push <remote> <local branch>:<remote branch>: send objects to remote, and update remote reference
	
