# Setting up a coding workspace
At CASR, we aim for consistency in how we set up and run Python code - we want every script to run seamlessly on any analyst’s machine, without worrying about different setups.
To help new team members get started, this guide will walk through how to set up a Python coding workspace using Visual Studio Code (VS Code) and a fast, modern tool called [`uv`](https://github.com/astral-sh/uv) for managing environments and packages.
> **Note**: These instructions assume your equipment is fairly standard. We use Windows, however, if you’re using a different setup (e.g., Mac, Linux, or personal machine), some steps may differ slightly.
---

## Step 1: Install Visual Studio Code (VS Code)
Microsoft's [VS Code](https://code.visualstudio.com/) is a lightweight and powerful editor for writing Python code.
1. Download VS Code from the [official download site](https://code.visualstudio.com/download) and install it.
2. Open VS Code. You’ll see a vertical sidebar on the left with several icons:
   - **Explorer** – View and open files and folders  
   - **Search** – Find keywords across files  
   - **Source Control** – Connect to Git repositories  
   - **Run & Debug** – Launch and monitor your code  
   - **Extensions** – Install new tools and plugins
3. Click on the **Extensions** icon (or press `Ctrl+Shift+X`)
4. Search for **Python** (by Microsoft) and click **Install**
5. (Optional) Also install **Pylance** for better type-checking and IntelliSense


## Step 2: Install `uv`
[`uv`](https://github.com/astral-sh/uv) is a modern, fast package manager for Python. 
The reason we use `uv` is because it calculates which versions of packages work together, then essentially freezes the package versions so that the code will always run. 
If the package versions are updated, their interdependencies may be broken and the code may not run properly.
1. Open a new terminal in VS Code (`Terminal > New Terminal` or ``` Ctrl + Shift + ` ```)
2. Run the following command:
   ```powershell
   powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
   ```
3. Close the terminal once installation completes (`X` button in top-right corner of terminal area).


## Step 3: Create a new project folder
1. Create a new folder to store your project
2. Open this folder in VS Code (`File > Open Folder`)
3. You’re now ready to initialise your project


## Step 4: Initialise a `uv` project
This sets up the project with a standard structure.
1. Open a new terminal in VS Code (`Terminal > New Terminal` or ``` Ctrl + Shift + ` ```)
2. Run:
   ```bash
   uv init
   ```
This will create:
- `main.py`: your main entry point for the script  
- `pyproject.toml`: stores metadata and dependencies  
- `.gitignore`: a default ignore list for Git


## Step 5: Create and activate a virtual environment
A virtual environment keeps your dependencies isolated from other projects.
1. Run:
   ```bash
   uv venv .venv
   ```
2. Activate it:
   ```bash
   .\.venv\Scripts\Activate.ps1
   ```
3. If you get a security warning, run:
   ```powershell
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   ```
   Then activate again:
   ```bash
   .\.venv\Scripts\Activate.ps1
   ```
You should now see `(.venv)` at the beginning of your terminal prompt. 


## Step 6: Install Python Packages
Install the packages you'll use for your project.
1. Use the following command to install a package:
   ```bash
   uv pip install ***[package-name]***
   ```
Example:
```bash
uv pip install pandas
```
Other commonly used packages include:
```bash
uv pip install matplotlib seaborn openpyxl scikit-learn pyodbc
```
These installations are tracked in `pyproject.toml` and `requirements.txt`.


## Step 7: Edit `main.py`
1. Use the **Explorer** tab to open `main.py`
2. You’ll see a basic template:
   ```python
   def main():
       print("Hello, from main.py!")

   if __name__ == "__main__":
       main()
   ```
Begin building your script by adding imports and writing functions within this file. If you want, you can start fresh by deleting the existing content.


## Step 8: Run Your Script
To run your project within the virtual environment:
```bash
uv run main.py
```
---

## Summary
With this setup, you're ready to:
- Use a consistent workflow with `uv`
- Write and run Python code in VS Code
Steps 1 & 2 are only done once per computer. For each new project, just repeat steps 3–8.
