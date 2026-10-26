# GitHub Repository Setup in Linux/Ubuntu Using VS Code



This guide explains how to configure Git in Linux/Ubuntu, connect to GitHub, clone a repository, create a new project folder, and push your changes to GitHub.



## Step 1: Check Whether Git Is Installed



Open your Ubuntu/Linux terminal and run:



```bash

git --version

```



If Git is not installed, install it using:



```bash

sudo apt update

sudo apt install git -y

```



Verify the installation:



```bash

git --version

```



## Step 2: Configure Your Git Identity



Configure your Git username and email using the name and email associated with your GitHub account.



```bash

git config --global user.name "Your GitHub Name"

git config --global user.email "your-email@example.com"

```



**\*\*Verify your Git configuration:\*\***



```bash

git config --global --list

```



## Step 3: Test Your GitHub Connection



If you have configured SSH authentication, test the connection using:



```bash

ssh -T git@github.com

```



If the connection is successful, GitHub will display a message confirming that you have authenticated successfully.



**\*\*Note:\*\*** If you are using HTTPS instead of SSH, you can skip this SSH connection test.



## Step 4: Create a Workspace and Clone Your Repository



Create a directory to store your GitHub projects:



```bash

cd \~

mkdir -p github_projects

cd github_projects

```



Clone your GitHub repository:



```bash

git clone https\://github.com/rishabhr136/DE_Linux_Vs_Code.git

```



Navigate into the cloned repository:



```bash

cd DE_Linux_Vs_Code

```



## Step 5: Verify the Repository Connection



Run the following commands to check your repository status, remote URL, and current branch:



```bash

git status

git remote -v

git branch

```



- `git status` — Displays the current state of your working directory.

- `git remote -v` — Shows the remote GitHub repository URL.



if nothing comes then git remote add origin https\://github.com/rishabhr136/DE_Linux_Vs_Code.git

- `git branch` — Displays your local branches.



## Step 6: Create a New Project Folder



You can create different project folders inside your repository.



### Example 1: Create a Snowflake Project Folder



```bash

mkdir -p snowflake_project

```



### Example 2: Create a Linux and VS Code Project Folder



```bash

mkdir -p linux_vs_code

```



Navigate into the folder:



```bash

cd linux_vs_code

```



## Step 7: Create a Markdown File



Create a Markdown file to document your Linux and VS Code setup:



```bash

touch git_linux_vs_code.md

```



You can edit this file using VS Code:



```bash

code git_linux_vs_code.md

```



If the `code` command is unavailable, open the repository folder in VS Code using **\*\*File → Open Folder\*\***.



Your repository structure will look like this:



```text

DE_Linux_Vs_Code/

├── linux_vs_code/

│   └── git_linux_vs_code.md

├── snowflake_project/

└── ...

```



## Step 8: Check Your Changes



Return to the repository root directory:



```bash

cd \~/github_projects/DE_Linux_Vs_Code

```



Check the status of your changes:



```bash

git status

```



Git should show the new folder and its Markdown file as untracked files.



## Step 9: Stage Your Changes



Add the new project folder and its contents to the Git staging area:



```bash

git add linux_vs_code/

```



Alternatively, stage all changes in the repository:



```bash

git add .

```



Verify the staged changes:



```bash

git status

```



## Step 10: Commit Your Changes



Create a commit with a meaningful message:



```bash

git commit -m "Add Linux and VS Code project documentation"

```



A commit records your staged changes in your local Git repository.



## Step 11: Push Your Changes to GitHub



Push your current branch to the remote repository:



```bash

git push -u origin master

```



The `-u` option sets the upstream branch, making subsequent pushes easier.



For future updates, you can generally use:



```bash

git add .

git commit -m "Update project documentation"

git push

```



**\*\*Note:\*\*** If you cloned the repository using HTTPS, GitHub authentication may require a personal access token or a configured credential manager. If you use SSH, your SSH key must be configured with GitHub.



## Step 12: Verify the Changes on GitHub



1\. Open your repository: [DE_Linux_Vs_Code on GitHub](https\://github.com/rishabhr136/DE_Linux_Vs_Code).

2\. Refresh the page.

3\. Open the `linux_vs_code` folder.

4\. Verify that `git_linux_vs_code.md` is visible.



Your project folder and its contents should now be available on GitHub.



## Important Notes



### 1. Git Does Not Track Empty Folders



Git tracks files, not empty directories. If you want to create a folder without any other files, add a placeholder file:



```bash

mkdir -p my_project

touch my_project/.gitkeep

git add my_project/

git commit -m "Add my_project directory"

git push

```



### 2. Always Run Git Commands From the Repository



Before running `git add`, `git commit`, or `git push`, verify that you are inside the correct repository:



```bash

pwd

git status

```



### 3. If You Get an Authentication Error



Check your remote URL:



```bash

git remote -v

```



For an SSH remote, the URL should look like:



```text

git@github.com:rishabhr136/DE_Linux_Vs_Code.git

```



For an HTTPS remote, it should look like:



```text

https\://github.com/rishabhr136/DE_Linux_Vs_Code.git

```



Choose the authentication method you have configured.



---



## Quick Reference: Complete Git Workflow



Once your repository is cloned and configured, use these commands for a typical update:



```bash

cd \~/github_projects/DE_Linux_Vs_Code



mkdir -p linux_vs_code

touch linux_vs_code/git_linux_vs_code.md



git status

git add linux_vs_code/

git commit -m "Add Linux and VS Code documentation"

git push -u origin master

```



**\*\*Workflow:\*\*** Clone → Create folder → Add files → Stage → Commit → Push → Verify on GitHub.



1\. Create and switch to a new branch

Replace my-code with your preferred branch name:

git checkout -b my-code



For example:

git checkout -b rishabh-code

then run git branch



2\. Add your project code

If your project is pyspark-zero-to-hero:

git add pyspark-zero-to-hero/



Don't use git add . because your repository is also seeing .vscode-server and .snowflake files.



Check what will be committed:

git status



3\. Commit

git commit -m "Add project code"



If you get the "Author identity unknown" error shown in your screenshot, first configure Git:

git config --global user.name "Rishabh Raj"

git config --global user.email "YOUR_GITHUB_EMAIL"



Then run:

git commit -m "Add project code"



4\. Push the new branch to GitHub

git push -u origin my-code



Or, if you used rishabh-code:

git push -u origin rishabh-code



Complete sequence

Assuming your project folder is pyspark-zero-to-hero:

git checkout -b rishabh-code

git add pyspark-zero-to-hero/

git status

git commit -m "Add project code"

git push -u origin rishabh-code



After the last command, your rishabh-code branch will be created on GitHub with your code.







You want to merge all changes from feature/project into abcde123 using the VS Code terminal. I'll explain each command, what it does, and how to handle merge conflicts safely.

1\. Understand the merge direction

Your goal is to bring the changes from the source branch into the target branch.

Source branch: feature/project



Contains the changes you want to bring over



Merge changes into

Target branch: abcde123



This branch receives the changes





Important: You must switch to abcde123 before running the merge command.

2\. Open the VS Code terminal

Open your repository in VS Code.

- Select Terminal → New Terminal.

- Ensure the terminal is running in your repository's root directory.

Check the location:

pwd

git rev-parse --show-toplevel





The second command prints the root directory of your Git repository.

3\. Check whether another merge is in progress

Run:

git status





If you see a message indicating that a merge is in progress, you have two choices:



- If you want to abandon the incomplete merge and start again, run:

git merge --abort





- If you have already resolved conflicts and want to finish the existing merge, continue resolving and committing it instead.



Caution: Don't automatically abort a merge if you have made conflict resolutions you want to keep.



If no merge is in progress, continue to Step 4.

4\. Fetch the latest branch information

git fetch origin





This downloads the latest branch and commit information from the remote repository named origin.



It updates your remote-tracking references, such as:

origin/abcde123

origin/feature/project





It does not automatically merge changes into your current branch or overwrite your working files.



Check whether the remote branches exist:

git branch -r





If origin/feature/project or origin/abcde123 does not appear, check the branch names and remote configuration before proceeding.

5\. Switch to the target branch

git switch abcde123





This changes your active branch to abcde123.



Verify:

git branch --show-current





Expected output:

abcde123





If Git refuses to switch because you have uncommitted changes, inspect git status first. Commit or safely stash your work rather than discarding it.



If abcde123 exists only on GitHub and not locally, use this instead:

git switch --track origin/abcde123





Only use that command if the local branch doesn't exist and the remote branch does.

6\. Update the target branch

Run:

git pull --ff-only origin abcde123





This updates your local abcde123 branch from GitHub when the update can be performed as a fast-forward.



The --ff-only option prevents Git from creating an unexpected merge commit during the pull. If it fails because the histories have diverged, stop and inspect the branch history rather than forcing the update.

7\. Merge feature/project into abcde123

This is the main command:

git merge origin/feature/project





Because you are currently on abcde123, Git merges the changes from origin/feature/project into that branch.



There are three common outcomes.

Case A: No conflicts



Git may report that it completed the merge. Continue to Step 8.







Case B: Merge conflicts



Git will list the files requiring manual resolution. Open those files in VS Code, resolve the conflicts, then stage and commit the result.







Case C: Already up to date



Git reports that the target branch already contains all commits reachable from the source branch. Check whether the intended changes are already present.







8\. Resolve conflicts in VS Code, if needed

A conflicted file may contain markers like these:

<<<<<<< HEAD

Code from abcde123

**=======**

Code from feature/project

\>>>>>>> origin/feature/project





These markers indicate competing changes:

- HEAD refers to the current target branch, abcde123.

- The section after ======= contains the incoming changes from feature/project.

- >>>>>>> marks the end of the incoming section.

VS Code may offer these actions:

- Accept Current Change — keep the target branch's version of that conflict.

- Accept Incoming Change — keep the incoming branch's version.

- Accept Both Changes — retain both sections, then check and edit the resulting code.

- Compare Changes — inspect the differences.

These choices apply to individual conflicts. Review the resulting file carefully, because accepting one side may discard useful changes from the other.



After resolving every conflict, run:

git add .

git status





Then complete the merge:

git commit -m "Merge feature/project into abcde123"





If Git already created the merge commit automatically, you don't need to create another one.

9\. Push the merged changes to GitHub

First, verify the current branch:

git branch --show-current





It should show:

abcde123





Then push:

git push origin abcde123





This publishes the merged commits to the remote abcde123 branch, assuming you have permission to push and the remote has not advanced in a way that requires further integration.

10\. Complete command sequence

If there is no merge in progress, both remote branches exist, and your working tree is clean, the normal sequence is:

# 1. Fetch latest remote information

git fetch origin



# 2. Switch to the target branch

git switch abcde123



# 3. Update the target branch safely

git pull --ff-only origin abcde123



# 4. Merge the source branch into the target

git merge origin/feature/project



# 5. Verify the merge

git status

git log --oneline --graph -10



# 6. Push the target branch to GitHub

git push origin abcde123





If Git reports conflicts at Step 4, resolve them in VS Code, stage the resolved files, commit the merge if required, and then push.



Remember: git merge origin/feature/project merges into whichever branch is currently checked out. In your case, that must be abcde123.



A merge conflict happens when Git cannot automatically decide which changes to keep—for example, when both branches edit the same lines in a file.

Step 1: Check which files have conflicts

In the VS Code terminal, run:

git status





You may see output like:

On branch abcde123

You have unmerged paths.



both modified:   sql/customer_query.sql

both modified:   python/transform.py





This means those files have changes on both branches that need your attention.

Step 2: Open the conflicted file in VS Code

Click a conflicted file in the VS Code Explorer or Source Control panel.



You'll see something like this:

EXAMPLE CONFLICT IN A FILE



Current change — abcde123

customer_status = "active"











Incoming change — feature/project

customer_status = "verified"









Git cannot determine which value is correct for your project.



The actual file may contain conflict markers like these:

<<<<<<< HEADcustomer_status = "active"=======customer_status = "verified">>>>>>> origin/feature/project







What each marker means:

- <<<<<<< HEAD — starts the code from your current branch, abcde123.

- ======= — separates the two versions.

- >>>>>>> origin/feature/project — ends the incoming version.

These markers are temporary. They must not remain in your final code.

Step 3: Choose how to resolve the conflict

VS Code typically displays buttons above the conflicting code.



Accept Current Change



Keeps the version from abcde123 for that conflict. Use it when the target branch's code is the version you want.









Accept Incoming Change



Keeps the version from feature/project for that conflict. Use it when the incoming implementation is the one you want.









Accept Both Changes



Keeps both versions. You may need to edit the combined result to avoid duplicate code or conflicting logic.









Resolve Manually



Edit the code yourself when both versions contain useful changes and need to be combined.







Important: You do not have to choose the same option for every conflict. Review each one according to your intended code behavior.



For example, if the correct result should use "verified", the final code should be:

customer_status = "verified"







Remove the conflict markers and save the file with Ctrl + S.

Step 4: Repeat for every conflicted file

In VS Code:

1\. Open the Source Control panel.

2\. Find the files listed under Merge Changes.

3\. Open each file.

4\. Resolve all conflict sections.

5\. Save each file.

Then check the status again:

git status





Make sure you have resolved all conflicts. If any files are still unmerged, return to those files and finish resolving them.

Step 5: Stage the resolved files

Once the conflicts are resolved, run:

git add .





This tells Git that your current file contents are the resolved versions you want to include in the merge.



Check:

git status





The files should no longer be listed as unmerged.

Step 6: Complete the merge

If Git has not already created the merge commit, run:

git commit -m "Resolve merge conflicts from feature/project"





If Git says there is nothing to commit or that the merge has already been completed, check git status before taking further action.

Step 7: Push the result to GitHub

Verify that you are on the target branch:

git branch --show-current





Expected:

abcde123





Then push:

git push origin abcde123





Your resolved changes will be uploaded to the target branch on GitHub.
