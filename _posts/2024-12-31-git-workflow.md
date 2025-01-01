---
title: Git workflow
date: "2024-12-31T21:41:41+09:00"
categories: [Knowledge, IT]
tags: [git, development]
description: 로그인 기능 개발을 위한 Git과 GitHub 워크플로우입니다.
author: hoon
---

## 1. 예시
가상의 시나리오입니다.

기존의 프로젝트 리포지토리를 클론 후 로컬에서 개발 한 뒤 머지하는 과정입니다.

| Step                                     | Example Code                                                  | Performed By | Description                                                      |
| :--------------------------------------- | :------------------------------------------------------------ | :----------- | :--------------------------------------------------------------- |
| 1. Clone the Repository                  | `git clone https://github.com/your-username/project-name.git` | Developer    | Clone the repository to the local machine to start working.      |
| 2. Create Feature Branch                 | `git checkout -b feature/login-function`                      | Developer    | Create a new branch for the feature development.                 |
| 3. Implement Functionality               |                                                               | Developer    | Implement the login functionality in the code.                   |
| 4. Stage Changes                         | `git add .`                                                   | Developer    | Stage the changes to prepare for commit.                         |
| 5. Commit Changes                        | `git commit -m "Implement login functionality"`               | Developer    | Commit the staged changes.                                       |
| 6. Push Changes                          | `git push origin feature/login-function`                      | Developer    | Push the committed changes to the remote repository.             |
| 7. Create Pull Request                   |                                                               | Developer    | Create a pull request to request a code review.                  |
| 8. Code Review Rejected                  |                                                               | Team Lead    | The pull request is rejected, and changes are requested.         |
| 9. Apply Requested Changes and Re-review |                                                               | Developer    | Apply the feedback, modify the code, and push the changes again. |
| 10. Sync with Main                       | `git pull origin main`                                        | Developer    | Pull the latest updates from the main branch.                    |
| 11. Resolve Conflicts and Push           | `git push origin feature/login-function`                      | Developer    | Resolve any conflicts and push the changes.                      |
| 12. Notify for Final Review              |                                                               | Developer    | Notify the reviewer to perform the final code review.            |
| 13. Merge Pull Request                   |                                                               | Team Lead    | Merge the pull request into the main branch.                     |

### 1.1. key takeaways
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