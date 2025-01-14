---
title: Git basics
date: "2024-12-31T21:41:41+09:00"
categories: [Knowledge, IT]
tags: [git, development]
description: 
author: hoon
published: false
---


## 1. 예시 1
로그인 기능 개발을 위한 Git과 GitHub 워크플로우입니다.

기존의 프로젝트 리포지토리를 클론 후 로컬에서 개발 한 뒤 머지하는 과정입니다.

작업 중

### 1.1. key takeaways
![alt text](/assets/img/posts/2024-12-31-git-workflow-remote-repositories.png){: width="400"}

- `origin`: 클론한 원격 저장소의 기본 이름. Connection 이름(url보다 간편). 따라서 다른 이름으로 바꿔도 무관함. 예) `git remote rename origin upstream`
- `git push origin [로컬 브랜치 이름]`: 로컬 브랜치를 `origin`에 밀어넣기(게시, 생성, 업데이트) 하는 것
- `git pull origin main`: 원격 저장소를 가져와서 로컬 저장소의 main 브랜치에 덮어 씌우기

## 2. Merging Using Git Command Line (Without Pull Request)

| Step | Action                                                 | Git Command                                                 | Description                                                                                          |
| :--- | :----------------------------------------------------- | :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------------- |
| 1    | Switch to the `main` branch                            | `git checkout main`                                         | Ensure you are on the `main` branch before merging.                                                  |
| 2    | Pull the latest changes from the remote `main` branch  | `git pull origin main`                                      | Update your local `main` branch to make sure it's up-to-date with the remote repository.             |
| 3    | Merge the feature branch into `main`                   | `git merge feature/login-function`                          | Merge changes from the feature branch (e.g., `feature/login-function`) into `main`.                  |
| 4    | Resolve any merge conflicts (if needed)                | Edit conflicting files, then `git add <file-with-conflict>` | If there are any merge conflicts, manually resolve them in the affected files and stage the changes. |
| 5    | Commit the merge                                       | `git commit`                                                | If there were conflicts, commit the resolved merge changes (Git auto-generates a commit message).    |
| 6    | Push the merged `main` branch to the remote repository | `git push origin main`                                      | Push the updated `main` branch (including the merged changes) to the remote repository.              |

## 3. 예시 2

### 3.1. **List and Create a Branch**:
- **List branches**: Use `git branch` to view all branches in the repository.  
- **Create a new branch**: Use `git branch PATCH-1-ADDING-HEADERS` to create a branch named `PATCH-1-ADDING-HEADERS`.  

### 3.2. **Switch Branch, Make Changes, and Merge**:
#### 3.2.1. Switch to a new branch and make changes:  
- Use `git checkout PATCH-1-ADDING-HEADERS` to switch to the new branch.  
- Use `git status` to check the working directory status.  
- Use `git add index.html` to stage the file(s) for commit.  
- Use `git commit -m "commit message"` to commit the changes.  

#### 3.2.2. Merge with the main branch:  
- Switch to the main branch with `git checkout main`.  
- Merge changes from the new branch using `git merge PATCH-1-ADDING-HEADERS`.  

#### 3.2.3. Push to the remote repository:  
- Push the updated main branch to the remote repository using `git push -u origin main`. 