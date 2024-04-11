

# Live exercise

#### Intro

Let's start by creating a local repository:

```
$ mkdir my_first_repo
$ cd my_first_repoi
$ git init
$ git status
```

and adding out first file, remember to use often `git status`.
```                   
$ touch first_file
$ git status
$ git add first_file
$ git status
$ git commit -m "my first commit"
$ git status
```

Now create your remote repository on GitHub/GitLab, call it "my_first_repo", and, if you haven't done it, configure your git:

``````
$ git config --global user.email youremail@domain.com
$ git config --global user.name "yourname"
$ git config --list
``````

In the case you haven't already done it, create an ssh key:

`````` 
$ ssh-keygen
``````

and add it inn your repository option (check the slides for extra information about this).

Now we are ready to connect our local repository with the remote one:


```
$ git remote add origin git@github.com:USERNAME/my_first_repo.git
$ git remote -v
```

Let's push upstream on the remote repository our master branch:

``` 
git push -u origin master
```

Now you have correctly initialized your repository.

if, instead, we wanted to start by cloning a repository we could have just done:

```````  
$ git clone git@github.com:USERNAME/my_first_repo.git
```````

#### Managing files

Let's modify the file we have:

``````
$ echo "this is my first file" >> first_file
$ git status
$ git add first_file
$ git status
$ git commit -m "first modification"
$ git status
$ git push
``````

Let's try adding other new files within a directory, and to modify again our first file, we will also check what has changed for the first_file:

``````
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
``````

Now we are going to add a directory that we do not want to add to the repository, we also modify some files:

``````
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
``````

`git status` keep reminding us about the ignored directory. Since this could be quite annoying, let's completely ignore that directory modifying the `.gitignore` as follows:

`````` 
$ git status
$ echo "dir_to_ignore/" >> .gitignore
$ cat .gitignore
$ git status
$ git add .gitignore
$ git commit -m "added .gitignore"
$ git push
``````

Let's try now to remove a file and to rename/move another file:

```````
$ git mv first_file file
$ git rm dir/file2
$ git status 
$ git commit -m "rm dir/file2 and rename first_file"
$ git push
```````
