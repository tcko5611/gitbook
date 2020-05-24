# Git

## Git init

### git init

* git init
* git init --bare

### git clone \[REPO\_URI\]

### remote repository\(exist\), no local repository

steps for writing code in remote repository 1. using **git clone "https://..."** to get remote repository code 2. Write your code in local repository 3. commit your code in local repository 4. push your code into remote repository 1. for non-empty remote repository, **git push origin master** 2. for empty remote repository, **git push -u origin master**

### remote repository with exist local repository

steps for check existing code in remote repository 1. **git remote add origin "https://..."** to connect local with remote 2. push your code into remote repository 1. for non-empty remote repository, 1. **git pull origin master** \(get the remote \) 2. **git push origin master** 2. for empty remote repository, **git push -u original master**

### git add and reset to unstage files, and revert the file

* git add &lt;files&gt; : to add file into stage
* git reset HEAD &lt;files&gt; : to unstage file
* git checkout HEAD &lt;files&gt; : revert the file

### git status

* **git status --untracked-files=no** or **git status -uno**

  **git revert file**

  ```text
  git checkout HEAD -- my-file.txt
  ```

  **git keywords in file**

* need to modify .git/info/attributes like follows:

  ```text
  # seee man gitattributes
  *.cpp ident
  ```

  then in \*.cpp will have:

  ```text
  /*
  * $Id: a3c0926847c6a014fabb59e85e70c8eeb25aff77 $
  */
  ```

### git branch

#### see all branch

> git branch

#### create branch

> git branch aa

#### checkout brach

> git checkout aa

#### git create and checkout branch

> git branch -b bb

#### git delete branch

> git branch -d bb

#### git merge branch

```text
git checlout master
git merge aa
```

## git tag

### simple tag

```text
git tag big_cats 51d54(commit number)
```

### annotated tag

```text
git tag big_cats 51d54 -a -m "test for one"
```

### delete tag

```text
git tad -d big_cats
```



## part of the depository

```text
git clone --depth 1 --no-checkout --filter=blob:none \
  "file://$(pwd)/server_repo" local_repo
cd local_repo
git checkout master -- mydir/
```



## p4 syntax in git

| p4 | git |
| ---: | ---: |
| p4 opened | git status-uno |
| p4 edit | --- |
| p4 revert | git checkout HEAD |
| p4 submmit | git add \(git reset _--hard_\), git commit |

