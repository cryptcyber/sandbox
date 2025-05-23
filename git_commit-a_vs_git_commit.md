A close look at the man page of git commit describes it:

when you add a new file to git, you do it with git add. This marks the file to be added upon next commit (as well as git rm does for removing files).
when you change a file, you could also do git add to mark it for the next commit, but usually you don't. In that case, git commit -a does the work for you:


Tell the command to automatically stage files that have been modified and deleted, but new files you have not told git about are not affected

Example: if you want to do a commit with only some files that are modified (e.g. you fixed a bug affecting two files (one.c, two.c) and removed some typos in other files and want to have the bugfix checkin separate from the typo checkin), you would do a git add for each of the bugfix files and then commit:

git add one.c
git add two.c
git commit -m "bugfix checkin" 


Once this is done, you commit your typo fixes using
git commit -a -m "fixed typos"


Regarding staging:
git commit only commits files that were marked in some way to be committed. This could happen through git add or git rm for example. This marking-for-commit is called staging.

Suppose you have made two distinct logical changes to a file, but only want to commit one of them. If "git commit" always committed all files that it knows about, that act would be impossible. Thus, you need to stage each change before doing a commit. If you "git add filename", you are staging all changes in that file. BTW, "git stage" is a synonym for "git add", and it might make the actions clearer if you use "git stage" instead.

Think of the staging area (the index) as an intermediate area between your working directory and the repository. The commands "git add" and "git stage" move things into the index, and "git commit" moves them from the index to the repository. "git commit -a" does both steps at once. It is often very useful to be able to use the staging are to help refine your commits so that you commit exactly what you want.

See also the article "You could have invented git (and maybe you already have!)": it helps when you think of the index as a "patch database".

If you "add" a file (to the "index" also known as "the staging area"), you actually add the patch represented by the evolutions made in that file.
If you made new evolutions in the same file after adding it to the index, you are actually doing a new patch (with all the new evolutions since the last "git add"), and those new evolutions will need to be added (to the index, the "patch database") before you will be able to commit.
Or you can just make git commit -a in that case.


To understand the difference between git commit and git commit -a you do need to know about the index in git, also known as the staging area.

The index essentially stores the state of each file that will go into the next commit. (By "the state" here, I mean the exact contents of the file, which git identifies by its hash.) When you type git commit without any other parameters, git will make a new commit in which the states of all the files are exactly as in the index. This can be very different from your working tree, and that's a useful feature of git, as I'll explain below.

What git add really does is to "stage" a file, or add its current state to the index. It doesn't matter whether it was originally tracked or not, you're saying "in the next commit, I want this file to be present with exactly this content". It's important to realize that this records the content you want to add, and not the filename - that means that if you then carry on making changes to a file after you've staged it with git add you'll see output from git status that sometimes confuses people coming from other version control systems:
$ git status
[...]
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#   modified:   foo.txt
#
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#   modified:   foo.txt


... but that makes absolute sense if you know about the index. You can see the changes you've already staged (i.e. the difference between the state of files in the index and HEAD, which is typically the last commit on the branch you're working on) with:
$ git diff --cached


... and you can see all the changes that haven't been staged yet (i.e. the difference between your working copy and the index) with:
$ git diff


So why is this so useful? Essentially, the idea is that the history of your project is most useful when each commit just contains a logically grouped set of changes that make a well-defined improvement to the project. The smaller you can make these commits the better, since later the wonderful tool git bisect can quickly help you track down which change introduced a bug. Unless you're extraordinarily disciplined, in the process of fixing a bug, you'll often end up editing other files that you don't really need to change in order to fix the problem, or might end up fixing two logically different problems before deciding to commit your changes. In those cases the index can help you separate out those changes - just git add each file that contains changes that you want in the first commit, run git commit, then do the same to create the second commit. [1]

If you get used to doing this and like the workflow, you'll end up sometimes just wanting to stage some of the changes in a file and not others, in which case you'll want to find out about git add -p (and then its interactive s and e options!)

However, to get back to the original question - what does git commit -a do that's different? Essentially it says, "Before creating this commit, also stage every file that's been modified (or deleted) at its current state." So, if you don't think that carefully staging files in the index before committing is of use, you can just use "git commit -a" all the time. However, I think that one of the nice things about using git is that it encourages you create beautiful commits, and actively using the index is a great help for that. :)


Notes:

In order to keep the above explanation simple, some statements are somewhat approximate (e.g. I haven't mentioned that the index also stores the (non-)executable state of the file, that it can have multiple versions during a merge, etc. etc.)

[1] You should be careful doing this if you want to make sure that every commit in your history has been properly tested - if that's important in your project, you should test the tree at every new commit before pushing...

