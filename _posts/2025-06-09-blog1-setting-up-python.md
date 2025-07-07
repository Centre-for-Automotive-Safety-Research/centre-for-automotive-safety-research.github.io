# Getting setup to code with Python

There are a few steps required to get setup to start doing data analysis coding with Python. Somewhat confusingly, each step can be achieved through multiple methods and there is no single "proper way". Recommended methods may vary depending on what you intend to use Python for (e.g., data analysis, website development, machine learning) and whether you are using a Windows, Mac, or Linux operating system.

The methods that are outlined below should get you started, but we recommend that anybody who is just starting out do their own reading and research to gain a more in-depth understanding.

<!-- Simple explanations are provided for context, but this is not comprehensive -->

`Add some links to guides`


## Select an IDE
The first step is to select an integrated development environment or IDE. While it is certainly possible to write Python code into a simple text file, an IDE provides a bunch of advantages such as syntax highlighting (colour-coding of various parts of code to help readability) and error detection (automatic detection of errors in code). Most IDEs will also implement tools that help to view, organise, and run your Python code.

For our coding environment we typically use [Visual Studio Code](https://code.visualstudio.com/) (usually referred to as VS code) which is free, popular, and feature rich.

Guidance to download and install VS Code can be found on the software website. VS Code has a lot of features along with settings and customisations, so it is recommended that you take some time to check out a few guides to getting setup and comfortable with the interface. 

There are other many other IDE options if you're interesting in what else is out there. Some of the alternatives include [Zed](https://zed.dev/) (free software that focusses on being fast), [Subline Text](https://www.sublimetext.com/) (paid software with a clean look), [PyCharm](https://www.jetbrains.com/pycharm/) (paid software with dedicated Python features), or [Spyder](https://www.spyder-ide.org/) (free software with features for data analysis).

## Install Python
Once you have selected and installed an IDE, the next step is to install Python. There are many ways to do this and it is possible to install multiple versions of Python multiple times in multiple places on your computer. As shown in this classic [XKCD comic](https://xkcd.com/1987/), it is very easy to end up in a complete mess if you're not careful.

<p align="center">
<img src="https://imgs.xkcd.com/comics/python_environment.png" width="350">
</p>

Rather than installing Python directly, we recommend using a software called [uv](https://docs.astral.sh/uv/) which provides the ability to install a dedicated version of Python for each individual coding project (this concept is explained further in the next step).

To install uv on Windows, open a terminal (such as Command Prompt) and run the following command:

`powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`
 
To check that uv has been installed, close and reopen the terminal and run the command `uv`. This should output a help message about how to use uv.

While you have not quite yet installed Python, you're now all set up to do so in the next step.

## Utilise a virtual environment
Something that is not often mentioned when just starting out with coding in Python, is that it is important (best practice) to keep each of your projects isolated from one another. 

The standard way to ensure projects are isolated is to utilise what is called a 'virtual environment' which instals a dedicated standalone version of Python along with any external libraries you request (explained in the next step).

The full details about why this is important are beyond the scope of this introduction, but in simple terms, an isolated project means you can use different versions of Python and external libraries for each project without worrying about how it might your other projects.

Fortunately uv makes this process easy for us. To start a new project, create a project folder in an appropriate location, open a terminal, and run the command: `uv init`.

This will create five files:

 - `.gitignore` is used for source control (this will be useful later but can be ignored for now)
 - `.python-version` is used to define what Python version your project should use (uv will suggest the current version of Python, but you can change it to a [different version](https://devguide.python.org/versions/) here if you want)
 - `main.py` is a simple code sample to get you started (you can start writing your code by building upon what's there or delete it all to start from scratch - if you prefer, you can even delete `main.py` and start your own `.py` file with a different filename)
 - `pyproject.toml` is a file that uv uses to keep track of your project (you can ignore it)
 - `README.md` is a file where you can keep notes about your project (it's written in [Markdown](https://en.wikipedia.org/wiki/Markdown) language)

Now that you have set up your project with uv, you can run your code with the command: `uv run <file_name.py>`.

The first time you do this, uv will create one more file and a folder:

 - `uv.lock` is another file that uv uses to keep track of the status of your project (you can ignore it)
 - `.venv` is your virtual environment (we finally got there!)

Within `.venv` are a bunch of files that are necessary for the virtual environment, including a standalone version of Python. You can safely ignore this folder as uv will take care of making any changes that would be required.

In addition to creating the extra files, you will also have seen that uv did also actually run your code. You can now run your code again whenever you like with the same command, and it will go ahead without needing to install any new files.

## Manage libraries
Python comes with a lot of capability built in, but it becomes even more powerful when you take advantage of external libraries - code that has been written by others and designed for you to use.

However, before you can use an external library, you will need to install it into your virtual environment.

To add libraries to your virtual environment, run the command: `uv add <library_name>`.

You will see that uv goes out and installs whatever is required. To see what libraries you have installed, you can open the `pyproject.toml` file and they will be listed under 'dependencies'.

Some of the libraries we will probably be working with in the future include:

 - `numpy` - working with scientific data such as arrays and vectors
 - `pandas` - used for manipulation and analysis of tabular datasets
 - `matplotlib` - powerful plotting library for generating charts and figures
 - `scikit-learn` - statistical analysis and machine learning
 - `geopandas` - expands `pandas` with geospatial data tools
 - `osmnx` - accessing and analysing OpenStreetMap data

You should now be all set up to start writing Python code...