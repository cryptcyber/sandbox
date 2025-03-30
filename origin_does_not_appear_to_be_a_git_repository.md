# origin does not appear to be a git repository

The issue here is almost certainly caused by you running git init first and then cloning your project into the same directory using the git clone command. And even if you can fix the issue by adding the remote to the empty repository that you initialized on your machine, you don't want to do that because you will then have two git repositories, one nested inside the other.

Because of the above, if you don't have any valuable changes in the project on your local machine, it would make things more simple to just blow away all the contents of your ~/myfolder/ dir and re-clone the project by doing the following:
rm -rf ~/myfolder
git clone git@github.com:myname/myproject.git ~/myfolder


If you have changes that you want to preserve in your project located at ~/myfolder/mysubfolder, then we can just move stuff around to make sure that you don't lose anything. I would recommend first that you do the following to get rid of the nesting:
cd ~/myfolder
rm -rf .git
mv mysubfolder/* .


At this point, you should have all of your project's files and .git folder the mysubfolder/ directory. Which you can check with ls -al

Next we want to check that you have a remote repository properly configured. To do so, run the command git remote -v hopefully you will see the following output:
origin  git@github.com:myname/myproject.git (fetch)
origin  git@github.com:myname/myproject.git (push)


If you don't see the output above, don't have any fear, quick fix. Just run the command:

git remote add origin git@github.com:myname/myproject.git


At this point we have a reasonable directory structure and the remote repository properly configured for you. Now it's a matter of having access to that remote repository. Since the way you set it up originally was over ssh, the best option, and that's what I've shown you how to do so far, we are going to continue down that path.

First we need to check that you do have a keypair, so do the following:
cd ~/.ssh
ls


If you have a keypair then I would expect to see the two files id_rsa and id_rsa.pub. If you don't have those files (although sometimes they are named differently), then do the following
ssh-keygen -t rsa
ls


Now you should have those files. id_rsa is your private key, which you don't want to leave your computer and should never be shared with anyone, but id_rsa.pub is you public key, which is a way that other people and services know how to identify you. We need to make sure that your github account has you public key id_rsa.pub added to it. So now do the following:

login to github
Navigate to https://github.com/settings/keys
Click the button "New SSH key"
Give it a name like "Home Desktop" or "Work laptop"
Copy the contents of the file id_rsa.pub
Paste the contents of that file into the key field in github
Click the "Add SSH Key" button

Now you should have no trouble at all push and pulling from your remote repository.
