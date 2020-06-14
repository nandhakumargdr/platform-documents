# Git Deployment Guide

#####To Initialize git repository
```sh
$ git init
$ git add .
$ git add remote origin https://repository_link.git
$ git clone https://username@github.com/username(or)orgname/reponame.git

$ git remote add origin https://username@github.com/username(or)orgname/reponame.git
```

##### To Commit git repository
```sh
$ git commit -m "Initial Commit"
$ git push origin master
```

##### To remove remote origin
to list all remote url
```sh
$ git remote -v
```
```sh
$ git remote remove origin
```
