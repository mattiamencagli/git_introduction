

# Live exercise

## 0 - Before to start

Create an account on GiuHub or GitLab. Then, create your ssh key (in case you don't have one) and add it to your repository option (check the slides for extra information about this):
```
$ ssh-keygen
```

Then configure git as follows:
```
$ git config --global user.email youremail@domain.com
$ git config --global user.name "yourusername"
$ git config --global core.editor "vim"   // here you can put your favorite editor, nano is the default one
$ git config --list
```

If you are familiar with the `.bashrc` file, you can create some aliases to speed up your work with git. For example, I always add the following line in my `.bashrc` files:
```
alias gitlogtot='git log --graph --oneline --all --decorate'
```
Moreover, you can customize the prompt of your terminal by modifying the `PS1` variable to include a line that gives you a glance at the status of your repository. Check the slides to achieve it.

## 1 - Intro

Let's start by creating a local repository:
```
$ mkdir my_first_repo
$ cd my_first_repoi
$ git init
$ git status
```

and adding `first_file`, remember to use often `git status`.
```                   
$ touch first_file
$ git status
$ git add first_file
$ git status
$ git commit -m "my first commit"    // (you can try not to use -m, and use your editor instead)
$ git status
```

Now create your remote repository on GitHub/GitLab, call it "my_first_repo".

Now we are ready to connect our local repository with the remote one:
```
$ git remote add origin git@github.com:USERNAME/my_first_repo.git
$ git remote -v
```

Let's push upstream on the remote repository the `master` branch, also remember to use quite often the command `git log --graph --oneline --all --decorate`:
``` 
$ git log --graph --oneline --all --decorate
$ git push -u origin master
$ git log --graph --oneline --all --decorate
```

Now you have correctly initialized your repository.

if, instead, we wanted to start by cloning a repository we could have just done:
```
$ git clone git@github.com:USERNAME/my_first_repo.git
```

## 2 - Managing files

Let's modify the file we have:
```
$ echo "this is my first file" >> first_file
$ git status
$ git add first_file
$ git status
$ git commit -m "first modification"
$ git status
$ git push
$ git log --graph --oneline --all --decorate
```

Let's try adding other new files within a directory, and to modify again our first file, we will also check what has changed for the first_file:
```
$ mkdir dir
$ touch dir/file1 dir/file2
$ ls dir/ 
$ echo "let's add a second line to my first file" >> first_file
$ cat first_file
$ git diff first_file
$ git status
$ git add -A
$ git status
$ git commit -m "add dir, modified first_file"
$ git push
```

Now we are going to add a directory that we do not want to add to the repository, we also modify some files:
```
$ mkdir dir_to_ignore
$ touch dir_to_ignore/ignored_file1 dir_to_ignore/ignored_file2
$ ls dir_to_ignore
$ echo "I am file 1" >> dir/file1
$ echo "I am file 2" >> dir/file2
$ git status
$ git add -u
$ git status
$ git commit -m "add dir, modified first_file"
$ git push
```

`git status` keeps reminding us about the ignored directory. Since this could be quite annoying, let's completely ignore that directory modifying the `.gitignore` as follows:
```
$ git status
$ echo "dir_to_ignore/" >> .gitignore
$ cat .gitignore
$ git status
$ git add .gitignore
$ git commit -m "added .gitignore"
$ git push
```

Let's try now to remove `file2` and rename/move `file`:
```
$ git mv first_file file
$ git rm dir/file2
$ git status 
$ git commit -m "rm dir/file2 and rename first_file"
$ git push
```

## 3 - Undoing thing

But what if we want to undo things? Let's try to un-stage and un-modify `file`:
```
$ echo "and here's the third line" >> file
$ git add file
$ git status
$ git restore --staged file      (or git reset HEAD file)   // un-stage
$ git status
$ git restore file       (or git checkout file)   //un-modify
$ cat file
```

And to undo the last (or 3 lasts) local commit?
```
$ echo "and here's the third line" >> file
$ git commit -a -m "3rd line added to file"
$ git status    // Your branch is ahead of 'origin/master' by 1 commit.
$ git log --graph --oneline --all --decorate
$ git reset HEAD~1      (or HEAD~3 for last 3 commits)
$ git status
$ git restore file
$ git log --graph --oneline --all --decorate
```

But What if you already `git push` but you want to come back to the previous commit?
```
$ echo "and here's the third line" >> file
$ git commit -a -m "3rd line added to file"
$ git push 
$ git status
$ git revert HEAD
$ git status
$ git restore file
$ git log --graph --oneline --all --decorate
```

And to revert two commits? You have to use a range: from 2 commits before `HEAD` (`HEAD~2`, or the name of the commit, something as `b2509f0`) to `HEAD` itself (`HEAD`, or for example `25b7eba`), as it follows:
```
$ echo "and here's the third line" >> file
$ git commit -a -m "3rd line added to file"
$ echo "and here's the fourth line" >> file
$ git commit -a -m "4rd line added to file"
$ git push 
$ git status 
$ git revert HEAD~2..HEAD
$ git status
$ cat file
$ git restore file
$ git log --graph --oneline --all --decorate
```

## 4 - Branches

Let's see now how to manage a new branch:
```
$ git branch add_newfeature
$ git checkout add_newfeature
$ git branch -a
$ git log --graph --oneline --all --decorate
$ mkdir newdir
$ touch newdir/newfeature_1 newdir/newfeature_2
$ git status
$ git add -A
$ git commit -m "add new feature in the new branch add_newfeature"
$ git log --graph --oneline --all --decorate
$ git branch -a
$ git push -u origin add_newfeature
$ git log --graph --oneline --all --decorate
$ git branch -a
```

Let's also create another branch that diverges from the `master`:
```
$ git checkout -b improve_file master
$ git log --graph --oneline --all --decorate
$ echo "this is the 3rd... for the 3rd time." >> file
$ git status
$ git commit -a -m "add 3rd line to file with branch improve_file"
$ git log --graph --oneline --all --decorate
$ git push -u origin add_newfeature
$ git log --graph --oneline --all --decorate
$ echo "this is the 4rd... again." >> file
$ git commit -a -m "add 4rd line to file"
$ git push
$ git log --graph --oneline --all --decorate
```

Now I am happy with the improvements on `file`. Therefore, it is time to merge the branch `improve_file` into the `master` branch. Since we do not have a divergent history between `improve_file` and `master` we will automatically do a fast-forward merge:
```
$ git checkout master
$ git merge improve_file     // Fast-forward (you can use --ff-only uf you really really want to be safe)
$ git push
$ git log --graph --oneline --all --decorate
```

I am also ok with the new feature added by the `add_newfeature` branch. So let's merge it onto the `master` branch. This time, since they have a divergent history, we will automatically do a commit merge:
```
$ git checkout master
$ git merge add_newfeature     // commit merge (if you use --ff-only you'll get an error this time because of the diverging history)
 // accept the commit from your default editor
$ git push
$ git log --graph --oneline --all --decorate
```

If now I restart working on `add_newfeature` or `improve_file` I won't have the commit I just did. This could be a problem in some cases, so let's move the two branches to also consider the new merge commit:
```
$ git checkout add_newfeature    
$ git merge master      // Fast-forward 
$ git checkout improve_file
$ git rebase master        // same as Fast-forward merge in this case
$ git log --graph --oneline --all --decorate
$ git push --all
$ git log --graph --oneline --all --decorate
```

However, I just changed my mind, and I think that the branch `improve_file` is not needed (`file` is already perfect as it is). So let's delete that branch, both locally and remotely (this could have been done also before the last passage of course):
```
$ git checkout master
$ git git branch -d improve_file
$ git log --graph --oneline --all --decorate
$ git push --prune --all      // (or git branch -r -d origin/improve_file)
```

Now Let's see how to avoid merge commits using the `git rebase` command. First of all, let's create 2 diverging commits for our two remaining branches (`master` and `add_feature`):
```
$ git checkout master
$ touch file2
$ git add file2
$ git commit -m "file2 added"
$ git checkout add_newfeature
$ touch newdir/newfeature_3
$ git add newdir/
$ git commit -m "newfeature_3 added"
$ touch newdir/newfeature_4
$ git add newdir/
$ git commit -m "newfeature_4 added"
$ git push --all
$ git log --graph --oneline --all --decorate
```

Now we are ready to `git rebase` `add_newfeature` onto the `master`!
```
$ git checkout add_newfeature
$ git rebase master
$ git log --graph --oneline --all --decorate
$ git status      // Your branch and 'origin/add_newfeature' have diverged
```

Before to worry about the `git push`, let's do the `git merge` locally:
```
$ git checkout master
$ git merge add_newfeature // Fast-forward (thanks to the rebase now we have a linear history of commits)
$ gitlogtot
```

And now the `git push`. For the master it is straightforward, but since `add_newfeature` is divergent with respect to its own remote branch we will have to do something different: either you `push --force` it in order to maintain the branch, or you delete the local branch and prune the remote one in the case the branch it is not necessary anymore:
```
$ git checkout master
$ git push --all       // master will be fine, add_newfeature will be rejected because its diverging history
$ git log --graph --oneline --all --decorate
option 1 $ git checkout add_newfeature
         $ git push --force-with-lease    // more safe then just --force
option 2 $ git branch -D add_newfeature      // -d would give a safety error: The branch 'add_newfeature' is not fully merged.
         $ git push --prune --all
```

## 5 - Working with more people

This part can be done in couple with someone else, or (in the case your collaborators are evil and do not like git) you can do it by yourself by cloning again your repository in another folder and opening two terminals: one will be in the directory where you initialize the repository and the other in another directory (DO NOT clone the repository within the repository itself!):
```
$ git remote -v     // copy the url
$ cd ..
$ git clone git@github.com:USERNAME/my_first_repo.git my_first_repo_2   // paste the url and choose a non default name for the folder
$ cd my_first_repo_2
```

Now that everything is set let's start.  
Let's say that Mario and Luigi are two colleagues. Luigi `git clone` Mario repository, work on it, and then `git push` on the common remote repository:
```
L$ git checkout -b Luigi_branch
L$ mkdir L_dir
L$ touch L_dir/file_L
L$ git add L_dir/
L$ git commit -m "friendA first commit"
L$ git push -u origin Luigi_branch
```

Since Mario knows that his colleague is working on his repository, he `git fetch` sometimes to be sure to not lose the work pushed on the remote repository by Luigi:
```
M$ git log --graph --oneline --all --decorate
M$ git fetch
M$ git log --graph --oneline --all --decorate
M$ git status     // Your branch is behind 'origin/master' by 1 commit, and can be fast-forwarded.
M$ git pull       // Fast-farward merge on the remote master
M$ git branch -a
```

Luigi pushed a new branch! Mario wants to check what he is doing in there, so he tracks that branch locally. He also tries to improve Lugi's work by modifying `Luigi_branch` branch:
```
M$ git checkout --track origin/Luigi_branch
M$ echo "I am mario, this is luigi's file" >> L_dir/file_L
M$ cat L_dir/file_L
M$ git status
M$ git commit -a -m "I modify friendA's file"
M$ git push
```

Luigi forgets to `git fetch` before working again on its own branch. He modifies `file_L` (which Mario also modified) and then tries to `git push`:
```
L$ git checkout Luigi_branch
L$ echo "I am luigi, this is my file" >> L_dir/file_L
L$ git commit -a -m "friendA bad commit"
L$ git push     //rejected. Read the hints from the terminal!
L$ git pull     // the fetch works, but the merge does not. Because we modify the same line of the same file, and this creates a conflict that F must solve!
```

Luigi, therefore, tries to resolve the issue with `rebase`:
```
L$ git checkout Luigi_branch
L$ git pull --rebase         // This will evidencite the conflict
L$ git status                // check the output carefully!
L$ cat L_dir/file_L    // there's new staff here!
```

The file suddenly contains extra information that git put in the file to evidence the conflicts between my friend's commit and my commit. 
```
  <<<<<<< HEAD
  I am mario, this is luigi's file               // this is the work of M$
  =======
  I am luigi, this is my file                            // this is the work of F$
  >>>>>>> 0bc2059 (friendA bad commit)        
```

Using your favorite editor, edit the file to solve the conflicts: you can delete what Mario did or what Luigi did, and you can also mix the two of them, in reality, Mario and Luigi should talk and work out this together. Remember also to remove the extra characters put by git, the final solution could be:
```
  I am mario, this is luigi's file.
  I am luigi, this is my file
  Now this is our file.
```

At this point let's end the rebase/merge:
```
L$ git checkout Luigi_branch
L$ git add -A
L$ git status
L$ git rebase --continue        // here you could also use git commit
L$ git push
L$ git log --graph --oneline --all --decorate
```

After that, Luigi decides to modify `file`, but forgets to immediately merge with the `master` branch and `push`:
```
L$ git checkout Luigi_branch
L$ echo "6th line!" >> file
L$ git commit -a -m "6th line to file"
L$ git push
```

But in the meanwhile, Mario has changed `file` in the `master` branch.
```
M$ git checkout master
M$ echo "this is the 5th line." >> file
M$ git commit -a -m "5th line to file"
M$ git push
```

This time the `push` works because they have pushed on different branches! Now Mario `pull` and notices that `Luigi_branch` has some modifications, so it pulls also those:
```
M$ git pull
M$ git checkout Luigi_branch
M$ git pull
```

Mario tries to merge the branch onto the `master`, creating a merge conflict, that he will have to resolve:
```
M$ git checkout master
M$ git merge Luigi_branch
M$ git log --graph --oneline --all --decorate
M$ git status
  // open file and, fix the merge as you like, and save it.
M$ git commit -a -m "5th and 6th lines now makes sense."
M$ git push
M$ git log --graph --oneline --all --decorate
```

## 6 - Pull request

On large codes, with a lot of people working on it, usually, the master/main branch contains the stable version of the code and it is protected. Check the slides to see how to protect the master branch.

Let's create and advance a new branch:
```
$ git checkout -b p_req
$ echo "new file" > p_req_file
$ git add p_req_file
$ git commit -m "p_req commit"
$ git push -u origin p_req
$ git log --graph --oneline --all --decorate
```

Now go to GitHub/GitLab and click on pull request.

Check the slides for visual informations about this procedure.

In the end, you will just merge bypassing the branch protection system. This is possible because you are alone in your repository, and GitHub allows you to "break the rules" in this specific case. If your repository is shared with someone else, and you select more than "1" in the field "required number of approvals before merging", your colleague will need to review your merge request and accept it. After that, you will be able to merge onto the `master`. After that, you can pull from your local repository:
```
$ git checkout master
$ git pull
```


