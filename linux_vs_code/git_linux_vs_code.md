Check whether Git is installed in ubuntu or linux:
git --version

If Git is missing, install it in linux or ubuntu:
sudo apt update
sudo apt install git -y


Configure your Git identity (use the name and email associated with your GitHub account):
git config --global user.name "Your GitHub Name"
git config --global user.email "your-email@example.com"


Verify:
git config --global --list


First, test the connection:
ssh -T git@github.com

Step 3: Clone your GitHub repository into Linux
Replace the example URL with your actual GitHub repository URL.
cd ~
mkdir -p github_projects
cd github_projects

git clone https://github.com/rishabhr136/DE_Linux_Vs_Code.git

Enter the repository folder:
cd YOUR_REPOSITORY


Check that Git is connected:
git status
git remote -v
git branch


Step 4: Create a new folder inside the repository
For example, suppose you want a folder called snowflake_project.
mkdir -p snowflake_project
cd snowflake_project



Step 4: Create a new folder inside the repository
For example, suppose you want a folder called linux_vs_code.
mkdir -p linux_vs_code
cd linux_vs_code


Create a md file inside it:
touch git_linux_vs_code.md

Your repository will look like this:
YOUR_REPOSITORY/
├── linux_vs_code/
│   └── git_linux_vs_code.md
└── ...