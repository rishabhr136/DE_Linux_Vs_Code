There are multiple ways to connect VS Code with Snowflake. The best method depends on what you want to do.
Option 1: Snowflake Extension for VS Code (Recommended)
This is the easiest way if you want an experience similar to the Snowflake web UI.
Step 1: Install the extension
1. Open VS Code.
2. Go to Extensions (Ctrl + Shift +X).
3. Search for Snowflake.
4. Install the official Snowflake Extension for Visual Studio Code.
Step 2: Get your connection details
From the Snowflake UI, click your username → Account and note:
- Account Identifier
- Username
- Password (or authentication method)
- Warehouse
- Database
- Schema
- Role
Example:
Account : xy12345.ap-south-1.aws
User    : rishabh
Warehouse : COMPUTE_WH
Database : DEV_DB
Schema : PUBLIC
Role : SYSADMIN

Step 3: Create a connection
Press
Ctrl + Shift + P

Type
Snowflake: Add Connection

Enter
Connection Name: DEV

Account:
xy12345.ap-south-1.aws

Username:
rishabh

Password:
********

Warehouse:
COMPUTE_WH

Database:
DEV_DB

Schema:
PUBLIC

Role:
SYSADMIN

Save.
Step 4: Open a SQL file
Create
test.sql

Example
SELECT CURRENT_USER();

SELECT CURRENT_DATABASE();

SELECT CURRENT_SCHEMA();

SELECT CURRENT_WAREHOUSE();

Click Run Query.

The results appear inside VS Code.


Option 2: Use SnowSQL in VS Code Terminal
Since you've used SnowSQL before, you can also run SQL from the integrated terminal.

Open the terminal:
Ctrl + `

Run:
snowsql

or
snowsql -a <account> -u <username>

After connecting:
USE DATABASE DEV_DB;

SELECT CURRENT_USER();

SELECT * FROM EMPLOYEE LIMIT 10;

Or execute a script:
snowsql -f load.sql

Option 3: Connect using the Snowflake Python Connector
Install the connector:
pip install snowflake-connector-python

Example:
import snowflake.connectorconn = snowflake.connector.connect(    user='username',    password='password',    account='xy12345.ap-south-1.aws',    warehouse='COMPUTE_WH',    database='DEV_DB',    schema='PUBLIC')cur = conn.cursor()cur.execute("SELECT CURRENT_VERSION()")for row in cur:    print(row)


Option 4: Use the Snowflake Web UI
If you only need to write and execute SQL interactively, the Snowflake web UI is often the simplest option.
Which option should you use?
Based on our previous conversations, you're working on Snowflake ETL pipelines, SQL scripts, and scheduled jobs. I recommend:
- VS Code + Snowflake Extension for writing, organizing, and running SQL.
- SnowSQL in the VS Code terminal for running deployment scripts and automation.
- Git integration in VS Code for version control.
- Use the Snowflake web UI to inspect query history, monitor warehouses, and review tables.
This combination is commonly used in production environments.



# VS Code Tutorial: Activity Bar

The **Activity Bar** is located on the left side of Visual Studio Code. It provides quick access to important tools for managing files, searching code, working with Git, debugging applications, and installing extensions.

## A. Activity Bar

### 1. Explorer

**Purpose:** Displays and manages project files and folders.

Example project structure:

```text
Project/
├── app.py
├── requirements.txt
├── src/
└── tests/
```

**Shortcut:**

```text
Ctrl + Shift + E
```

**Common uses:**
- Open files and folders.
- Create new files and directories.
- Rename, move, and delete files.
- Navigate through your project structure.

### 2. Search

**Purpose:** Searches for text across files in your project.

**Shortcut:**

```text
Ctrl + Shift + F
```

**Common uses:**
- Find SQL queries across multiple files.
- Search for variable or function names.
- Find where a function is used.
- Locate specific code or configuration settings.

### 3. Source Control

**Purpose:** Integrates Git into VS Code to help you manage version control.

**Shortcut:**

```text
Ctrl + Shift + G
```

**Common uses:**
- View modified and untracked files.
- Stage changes.
- Commit changes.
- Push and pull code.
- Create and switch branches.
- Review and resolve merge conflicts.

### 4. Run and Debug

**Purpose:** Runs applications and helps identify and fix code errors.

**Shortcut:**

```text
Ctrl + Shift + D
```

**Common uses:**
- Set breakpoints.
- Execute code step by step.
- Inspect variable values.
- Investigate errors during execution.
- Debug Python applications.

### 5. Extensions

**Purpose:** Installs and manages extensions that add functionality to VS Code.

**Shortcut:**

```text
Ctrl + Shift + X
```

**Common uses:**
- Install Python and Jupyter extensions.
- Install Remote - SSH to connect to Linux environments.
- Install Git-related extensions.
- Add tools for SQL, databases, and cloud development.

---

## Quick Reference

| Activity Bar tool | Shortcut | Main purpose |
|---|---|---|
| Explorer | `Ctrl + Shift + E` | Manage project files |
| Search | `Ctrl + Shift + F` | Search across files |
| Source Control | `Ctrl + Shift + G` | Manage Git changes |
| Run and Debug | `Ctrl + Shift + D` | Debug applications |
| Extensions | `Ctrl + Shift + X` | Install additional tools |

**Data Engineer tip:** In VS Code, you will frequently use Explorer to manage Python and SQL files, Search to find code across projects, Source Control to collaborate through GitHub, and Extensions to configure Python, Jupyter, SSH, and database development tools.

# VS Code Tutorial: Settings, Integrated Terminal, and Keyboard Shortcuts

## 1. Open VS Code Settings

**Shortcut:**

```text
Ctrl + ,
```

VS Code Settings allow you to customize the editor according to your preferences.

### Common Settings to Customize

- **Theme:** Change the appearance of VS Code.
- **Font Size:** Increase or decrease code text size.
- **Auto Save:** Automatically save file changes.
- **Tab Size:** Configure the number of spaces used for indentation.
- **Terminal Shell:** Choose the default integrated terminal shell.

### Example: Enable Auto Save

Navigate to:

```text
Settings → Files → Auto Save
```

Alternatively, open Settings using `Ctrl + ,` and search for `Auto Save`.

---

## 2. Integrated Terminal

VS Code includes an integrated terminal that lets you execute commands without leaving the editor.

**Shortcut:**

```text
Ctrl + `
```

The backtick character (`` ` ``) is usually located below the `Esc` key on a standard keyboard.

### Supported Terminal Shells

- Bash
- PowerShell
- Command Prompt (CMD)
- Git Bash
- Linux shells

The available shells depend on your operating system and installed software.

### Example: Run a Python Script

On Windows:

```bash
python script.py
```

On Linux or Ubuntu:

```bash
python3 script.py
```

### Why Is the Integrated Terminal Useful?

- Run Python scripts.
- Execute SQL-related command-line tools.
- Run Git commands such as `git status`, `git pull`, and `git push`.
- Install Python packages using `pip`.
- Activate virtual environments.
- Execute Linux commands when working with Ubuntu or WSL.

You can also use the integrated terminal to manage your Linux environment while working in VS Code.

---

## 3. Ctrl + P — Quick Open

**Shortcut:**

```text
Ctrl + P
```

Quick Open allows you to search for and open files in your currently opened folder or workspace without manually navigating through the Explorer.

### Example

Suppose your project contains:

```text
data_engineering/
├── main.py
├── customer.sql
├── requirements.txt
└── src/
    └── transform.py
```

Press `Ctrl + P` and type:

```text
transform.py
```

Select the matching result to open the file.

### Why Use Quick Open?

- Open files quickly in large projects.
- Find Python, SQL, and configuration files.
- Switch between files without using the mouse.
- Improve productivity when working with multiple scripts.

**Tip:** Use `Ctrl + P` to open files and `Ctrl + Shift + P` to search for VS Code commands.

---

## 4. Useful VS Code Keyboard Shortcuts

| Shortcut | Purpose |
|---|---|
| `Ctrl + P` | Quick Open files |
| `Ctrl + ,` | Open Settings |
| `Ctrl + \` | Split the editor |
| `Ctrl + 1` | Focus the first editor group |
| `Ctrl + 2` | Focus the second editor group |
| `Ctrl + \`` | Open or close the integrated terminal |
| `Ctrl + Shift + P` | Open the Command Palette |
| `Ctrl + Page Up` | Switch to the previous editor tab |
| `Ctrl + Page Down` | Switch to the next editor tab |
| `Ctrl + Shift + 5` | Split the terminal in many default configurations |

### Split the Editor

Use:

```text
Ctrl + \
```

This lets you view two files side by side.

For example, you can view `main.py` in one editor group and `transform.py` in another.

### Focus Editor Groups

Use:

```text
Ctrl + 1
Ctrl + 2
```

These shortcuts move keyboard focus to the first and second editor groups, respectively, when those groups exist.

### Split the Terminal

Use the terminal's **Split Terminal** icon or the configured keyboard shortcut.

Splitting the terminal allows you to run commands in multiple terminal panes at the same time.

For example:
- Terminal 1: Run a Python script.
- Terminal 2: Execute Git commands.
- Terminal 3: Monitor logs or run Linux commands.

---

## 5. Quick Reference

| Task | Shortcut or setting |
|---|---|
| Open Settings | `Ctrl + ,` |
| Enable Auto Save | Settings → Files → Auto Save |
| Open Integrated Terminal | `Ctrl + `` |
| Search and open a file | `Ctrl + P` |
| Open Command Palette | `Ctrl + Shift + P` |
| Split the editor | `Ctrl + \` |
| Focus first editor group | `Ctrl + 1` |
| Focus second editor group | `Ctrl + 2` |
| Split the terminal | Split Terminal icon or configured shortcut |

**Data Engineer tip:** Learn `Ctrl + P`, `Ctrl + Shift + P`, and `Ctrl + `` first. These shortcuts help you navigate large projects, access VS Code commands, and run Python, SQL, Git, and Linux commands efficiently.