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
