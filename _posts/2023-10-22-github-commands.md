---
title: "GitHub Commands"
date: "2023-06-03 00:00:00 +0800"
categories: [DevOps]
tags: [github]
---


# Git and GitHub Commands Cheatsheet



## Login to GitHub
- **most common problem is login in to multiple github accounts in one system.**
    **To solve this problem we can use github cli or token**

- **Install Github Cli**
    - On Linux:
      ```bash
      sudo apt-add-repository -u https://cli.github.com/packages
      sudo apt install gh
      ```

    - On Mac:
      ```bash
      brew install gh
      ```


    - On windows:
      ```bash
      winget install gh
      ```

### Login to GitHub with cli

- **Now login to your github account using github cli**
  ```bash
  gh auth login
  follow instructions
  ```

  - login with web browser
    ```bash
    gh auth login --web
    ```
  - login with token
    ```bash
    gh auth login --with-token [your-token]
    ```


### Without the the use of github cli

- **Generate a Personal Access Token:**

  ```
  Go to your GitHub account settings.
  Navigate to "Developer settings" > "Personal access tokens."
  Click on "Generate token" and follow the instructions to create a new token.

  ```
- **Open your terminal and use the following command to set up your GitHub username and token:**
```bash
git clone https://<your-token>@github.com/<username>/<repository>.git
```

## Git Commands

- Initializes a new Git repository
```bash
 git init
  ``` 
-  Creates a copy of a remote repository
```bash
git clone [repository_url]  
```
- add multiple files


  ```bash
  git add file1 file2 file3
  ```

-   add all files in the current directory
  ```bash
    git .
  ```


- Records the changes made to the files
```
git commit -m "commit message"
```

- Modify your most recent commit
```bash
git commit --amend
git commit --am "Your message"
```


- Check repository status
```bash
  git status
  ```


- Sends the committed changes of the local repository to a remote repository
```
git push
```

-  Fetches and merges changes from the remote repository to the local repository
```bash
git pull
```

- Lists all the branches in the repository
```bash
git branch
``` 

- Create a new branch
```bash
git branch [branch_name]
```

- Switch to an existing branch
 ```bash
 git checkout [branch_name]
 ```




- create and checkout in one command

  ```bash
  git checkout -b [branch_name]
  ```


- Delete a local branch
```bash
git branch --d [branch_name]
```
<!-- git push origin --d [remote branch name]: Delete a remote branch -->

- Delete a remote branch
```bash
git push origin --d [remote_branch_name]
```

- Merge a branch into the active branch

  ```bash
  git merge [branch_name]
  ```

- Rename a branch
```bash
git branch -m [new_branch_name]
```

- View commit history
```bash 
git log
```

- List remote repositories
```bash
git remote -v
```


## GitHub Commands

- Adds a remote repository
```bash
git remote add origin [url]
```

- View remote repository
```bash
git remote show origin 
```


- Remove remote repository
```bash
git remote rm origin
```

- Changes the URL of an existing remote repository
```bash
git remote set-url origin [url]
```

- Pushes a branch to your remote repository
```bash
git push -u origin [branch]
```

- Deletes a remote branch
```bash
git push origin --delete [branch_name]
```

- Pulls remote branch
```bash
git pull origin [branch_name]
```

- View all remote branches
```bash
git branch -r
```

- Check differences between branches
```bash
git diff [branch1] [branch2]
```

- Undo the last commit (while keeping changes)
```bash
git reset HEAD^
```

- Discard changes in the working directory for a specific file
```bash
git checkout -- [file]
```

- Show changes made in a specific commit
```bash
git show [commit_hash]
```

- Create a new branch and switch to it
```bash
git checkout -b [new_branch]
```

- Rename a branch
```bash
git branch -m [new_branch_name]
```

- Undo the last commit and remove changes
```bash
git reset --hard HEAD^
```

- View all remote branches
```bash
git branch -r
```
