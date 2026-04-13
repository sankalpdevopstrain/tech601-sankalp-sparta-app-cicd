## CI&CD GitHub Setup - Sparta App

- [CI\&CD GitHub Setup - Sparta App](#cicd-github-setup---sparta-app)
  - [Purpose](#purpose)
- [1. Preparing the New Repository](#1-preparing-the-new-repository)
- [2. SSH Key Setup](#2-ssh-key-setup)
- [3. Git Setup and Initial Commit:](#3-git-setup-and-initial-commit)
- [4. Verification:](#4-verification)

### Purpose

This repository contains a copy of the Sparta Node.js application prepared for CI/CD pipeline integration using Jenkins. The app/ folder is placed at the root of the repository, and all code is uncompressed. This documentation outlines the complete step-by-step process for setting up the repo, configuring SSH access, and pushing the code to GitHub.

---
## 1. Preparing the New Repository
   1. Navigate to your local workspace:
        ```bash
        cd ~/github/tech601--learning
        ```
   2. Rename the existing folder for clarity:
        ```bash
        mv CI_CD tech601-sparta-test-app-cicd
        cd tech601-sparta-test-app-cicd
        ```
   3. Copy the full `app/` folder from the existing Sparta app:
        ```bash
        cp -r /c/Users/sanka/tech601-sparta-app/app .
        ```
   4. Verify the folder structure:
        ```bash
        ls app
        ```
   5. Output:
        ```bash
        README.md
        app.js
        package.json
        package-lock.json
        public/
        views/
        seeds/
        test/
        ```
   6. Remove `node_modules` to keep the repo lightweight:
        ```bash
        rm -rf app/node_modules
        ```
   7. Create a .gitignore file to prevent unnecessary files from being pushed:
        ```bash
        nano .gitignore
        ```
---
## 2. SSH Key Setup
   1. Generate an SSH key pair:
        ```bash
        ssh-keygen -t rsa -b 4096 -C "sankalpdevopstrain@gmail.com"
        ```
   2. Start the SSH agent:
        ```bash
        eval `ssh-agent -s`
        ```
   3. Output:
        ```bash
        Agent pid 1902
        ```
   4. Add your private key to the agent:
        ```bash
        ssh-add ~/.ssh/tech601-sankalp-github-key
        ```
   5. Output:
        ```bash
        Identity added: /c/Users/sanka/.ssh/tech601-sankalp-github-key (sankalpdevopstrain@gmail.com)
        ```
   6. Test SSH connection to GitHub:
        ```bash
        ssh -T git@github.com
        ```
   7. Output:
        ```bash
        Hi sankalpdevopstrain/tech601-sankalp-sparta-app-cicd! You've successfully authenticated, but GitHub does not provide shell access.
        ```
---
## 3. Git Setup and Initial Commit:
   1. Check if any remote is set:
        ```bash
        git remote -v
        ```
   2. Output:
        ```bash
        none
        ```
   3. Add GitHub repository as remote using SSH:
        ```bash
        git remote add origin git@github.com:sankalpdevopstrain/tech601-sankalp-sparta-app-cicd.git
        ```
   4. Add all files to staging:
        ```bash
        git add .
        ```
   5. Commit the files:
        ```bash
        git commit -m "Initial commit - Sparta app for CICD"
        ```
   6. Output:
        ```bash
        [master (root-commit) a224529] Initial commit - Sparta app for CICD
        14 files changed, 4832 insertions(+)
        ```
   7. Rename branch to main:
        ```bash
        git branch -M main
        ```
   8. Push to GitHub:
        ```bash
        git push -u origin main
        ```
   9.  Output:
        ```bash
        Enumerating objects: 26, done.
        Counting objects: 100% (26/26), done.
        Delta compression using up to 16 threads
        Compressing objects: 100% (19/19), done.
        Writing objects: 100% (26/26), 112.20 KiB | 1.60 MiB/s, done.
        Total 26 (delta 3), reused 0 (delta 0), pack-reused 0 (from 0)
        To github.com:sankalpdevopstrain/tech601-sankalp-sparta-app-cicd.git
        [new branch]      main -> main
        branch 'main' set up to track 'origin/main'.
        ```
---
## 4. Verification:
   1. Check GitHub repository in browser:
   2. Verify remote uses SSH:
        ```bash
        git remote -v
        ```
   3. Output:
        ```bash
        origin  git@github.com:sankalpdevopstrain/tech601-sankalp-sparta-app-cicd.git (fetch)
        origin  git@github.com:sankalpdevopstrain/tech601-sankalp-sparta-app-cicd.git (push)
        ```

