---
title: push-to-git
date: 2019-11-20 23:02:36
categories:
- command
---

<blockquote class="blockquote-center">push update to github repo
</blockquote>

### for first use

``` 
git init   ***init .git***  
git status   ***check files to be added to the local repository***  
git add xxx  ***add files to repo***   
git commit -m "commit description"  ***commit to repo***   
git remote add origin git@github.com:mobinets/hyblora.git  ***link local repo to github***    
git pull --rebase origin master  ***pull = fetch + merge***  
git push -u origin master  ***upload***  
```

### not first use repo

``` 
git add xxx  
git commit -m "commit discription"  
git push -u origin master  
```


