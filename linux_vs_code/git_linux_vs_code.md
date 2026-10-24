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

**Verify your Git configuration:**

```bash
git config --global --list
```

## Step 3: Test Your GitHub Connection

If you have configured SSH authentication, test the connection using:

```bash
ssh -T git@github.com
```

If the connection is successful, GitHub will display a message confirming that you have authenticated successfully.

**Note:** If you are using HTTPS instead of SSH, you can skip this SSH connection test.

## Step 4: Create a Workspace and Clone Your Repository

Create a directory to store your GitHub projects:

```bash
cd ~
mkdir -p github_projects
cd github_projects
```

Clone your GitHub repository:

```bash
git clone https://github.com/rishabhr136/DE_Linux_Vs_Code.git
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

if nothing comes then git remote add origin https://github.com/rishabhr136/DE_Linux_Vs_Code.git
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

If the `code` command is unavailable, open the repository folder in VS Code using **File → Open Folder**.

Your repository structure will look like this:

```text
DE_Linux_Vs_Code/
├── linux_vs_code/
│   └── git_linux_vs_code.md
├── snowflake_project/
└── ...
```

## Step 8: Check Your Changes

Return to the repository root directory:

```bash
cd ~/github_projects/DE_Linux_Vs_Code
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
git push -u origin HEAD
```

The `-u` option sets the upstream branch, making subsequent pushes easier.

For future updates, you can generally use:

```bash
git add .
git commit -m "Update project documentation"
git push
```

**Note:** If you cloned the repository using HTTPS, GitHub authentication may require a personal access token or a configured credential manager. If you use SSH, your SSH key must be configured with GitHub.

## Step 12: Verify the Changes on GitHub

1. Open your repository: [DE_Linux_Vs_Code on GitHub](https://github.com/rishabhr136/DE_Linux_Vs_Code).
2. Refresh the page.
3. Open the `linux_vs_code` folder.
4. Verify that `git_linux_vs_code.md` is visible.

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
https://github.com/rishabhr136/DE_Linux_Vs_Code.git
```

Choose the authentication method you have configured.

---

## Quick Reference: Complete Git Workflow

Once your repository is cloned and configured, use these commands for a typical update:

```bash
cd ~/github_projects/DE_Linux_Vs_Code

mkdir -p linux_vs_code
touch linux_vs_code/git_linux_vs_code.md

git status
git add linux_vs_code/
git commit -m "Add Linux and VS Code documentation"
git push -u origin master
```

**Workflow:** Clone → Create folder → Add files → Stage → Commit → Push → Verify on GitHub.