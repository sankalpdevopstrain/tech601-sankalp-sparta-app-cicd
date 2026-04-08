## Jenkins CI/CD pipeline

## Trying to get Job 3 working
final fix

## Job 1:

### Step 1: Create a New Jenkins Job
1. Open your Jenkins dashboard
2. Click “New Item”
3. Enter a name (e.g. job1-ci-test)
4. Select Freestyle Project
5. Click OK

### Step 2: Configure Source Code (GitHub Repo)
1. Scroll to Source Code Management (SCM)
2. Select Git
3. Paste your GitHub repository URL
4. Add credentials if required (SSH key)

At this point, Jenkins is set to: Clone your repository automatically when the job runs

### Step 3: Understand Default Working Directory
1. Jenkins starts inside the root of the cloned repo
2. Your application is inside an app folder: So you must manually navigate into it during the build step

### Step 4: Add Build Step (Execute Shell)
1. Scroll to Build
2. Click Add Build Step
3. Select Execute Shell
4. Step 5: Add Commands to Run Unit Tests
```bash
cd tech601-sparta-test-app-cicd/app
npm install
npm test
```
### Step 7: Save the Job
1. Click Save

### Step 8: Run the Job
1. Go back to Dashboard
2. Click your job
3. Click Build Now

### Step 9: Observe Build Queue & Execution
1. Job enters the Build Queue
2. Jenkins assigns it to an agent node
3. Status changes from:
    Queued → Running

### Step 10: Check Console Output
1. Click the build number (e.g. #1)
2. Click Console Output

You will see:
```bash
Git clone process
cd app execution
npm install logs
npm test results
```
---
## Blockers
During the setup and execution of the CI/CD pipeline using Jenkins, I encountered several issues. I investigated and resolved them as follows:

---

### Blocker 1: Missing models Folder

**Issue:**
Error: Cannot find module '../models/post'

**Cause:**
- The models/ directory was not copied into the CI/CD repository  
- The seed.js script (run during npm install) depends on this folder  

**Resolution:**
```bash
cp -r /c/Users/sanka/tech601-sparta-app/app/models app/
git add .
git commit -m "Fix Jenkins build - add missing models folder"
git push
```
### Blocker 2: Missing app.js

**Issue:**
Error: Cannot find module '../app'

**Cause:**

- The main application file app.js was missing from the repository
- The test suite (test-server.js) requires this file

**Resolution:**
```bash
cp /c/Users/sanka/tech601-sparta-app/app/app.js app/
git add .
git commit -m "Fix Jenkins build - add missing app.js"
git push
```

**Outcome:**
Tests were able to locate and run the application correctly.

### Blocker 3: SSH Authentication Failure

**Issue:**
git@github.com
: Permission denied (publickey)

**Cause:**

SSH agent was not running or the key was not loaded

**Resolution:**
```bash
eval `ssh-agent -s`
ssh-add ~/.ssh/tech601-sankalp-github-key
ssh -T git@github.com
git push
```

**Outcome:**
```bash
Successful authentication and ability to push changes to GitHub.
```

### Final Outcome

After resolving the above blockers:
```bash
Jenkins successfully cloned the repository via SSH
Dependencies installed correctly
Tests executed successfully
Build completed with status: SUCCESS
```

---


## Webhook Setup (Triggering the Pipeline)

### Step 1: Configure Webhook in GitHub
1. Go to your GitHub repository  
2. Click **Settings → Webhooks**  
3. Click **Add Webhook**  

Fill in the following:
- Payload URL:  
  http://<jenkins-ip>:8080/github-webhook/
- Content type: application/json  
- Events: Just the push event  

Click **Add Webhook**

---

### Step 2: Configure Jenkins Job 1
1. Open Job 1 in Jenkins  
2. Go to **Build Triggers**  
3. Enable:
   - GitHub hook trigger for GITScm polling  

---

### Outcome
- Every time I run `git push`, Jenkins is automatically triggered  
- Job 1 runs without needing manual input  
- This forms the start of the CI pipeline

---

## Job 2: CI Merge (dev → main)

### Purpose
The purpose of Job 2 is to automatically merge code from the `dev` branch into the `main` branch after Job 1 (testing) is successful.

This ensures that only tested code is merged into the main branch.

---

### Step 1: Create and Switch to dev Branch

I created a new branch called `dev` to simulate a developer making changes:

```bash
git branch dev
git branch
```

**Output:**
```bash
dev
main
```
### Step 2: Switch to dev Branch
`git switch dev`

**Output:**
Switched to branch `dev`

**To confirm:**
`git branch`

**Output:**
```bash
dev
main
```

### Step 3: Make a Change

I edited a file (README.md) to simulate a code change:
```bash
nano README.md
```

### Step 4: Check Status
`git status`

**This showed:**
`README.md was modified`

### Step 5: Stage and Commit
```bash
git add .
git commit -m "doc: change heading"
```
### Step 6: Push dev Branch

**First attempt:**
```bash
git push
```
Error:
```bash
No upstream branch set
```
Fix:
```bash
git push --set-upstream origin dev
```
Outcome of Git Steps
```bash
dev branch successfully pushed to GitHub
```
Ready to trigger the CI pipeline using webhook

---
## Job 2
### Step 1: Create a New Jenkins Job
1. Go to Jenkins dashboard
2. Click New Item
3. Enter name: sankalp-job2-ci-merge
4. Select Freestyle Project
5. Click OK

### Step 2: Configure Source Code (GitHub Repo)
1. Scroll to Source Code Management (SCM)
2. Select Git
3. Enter repository URL:`git@github.com:sankalpdevopstrain/tech601-sankalp-sparta-app-cicd.git`
4. Add credentials:
   - Kind: SSH Username with private key
   - Username: git
   - Private key: Content of `tech601-job2-jenkins-key`
5. Branch to build:`*/dev`

### Step 3: Configure Job Trigger (Link Job 1 → Job 2)

1. In Job 1 (sankalp-job1-ci-test):
   1.  Go to Post-build Actions
   2.  Select: Build other projects
   3.  Enter: sankalp-job2-ci-merge

This ensures:
- Job 2 only runs if Job 1 is SUCCESS

### Step 4: Add Build Step (Merge dev into main)
1. Scroll to Build → Execute Shell
- Add: Set Git author for merge commit
  - git config user.name "Sankalp Hiregoudar"
  - git config user.email "sankalpdevopstrain@gmail.com"

2. Fetch latest branches
```bash
git fetch origin
```
3. Ensure main is up-to-date locally
```bash
git checkout main
git reset --hard origin/main
```

4. Merge dev into main
```bash
git merge origin/dev
```

6. Push main back to GitHub
```bash
git push origin main
```

### Step 5: Save and Run Pipeline
1. Make a change in dev branch
2. Run:
```bash
git add .
git commit -m "test job2 pipeline"
git push origin dev
```

**Final Outcome**
- Webhook triggers Job 1
- Job 1 runs tests
- If tests pass → Job 2 runs automatically
- Code is merged from dev → main

This creates a basic CI pipeline with automated merging.

### Blocker: SSH Authentication Failure in Job 2
**Issue**
```bash
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```
**Cause**
- Jenkins Job 2 was using an SSH key that did not have write access to the GitHub repository
- GitHub rejected the connection during:
```bash
git fetch origin
git push origin main
```

---
## Job 3

Added for testing Job 3 pipeline deployment.

### Blocker


### Blocker 2
