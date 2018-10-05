## Intro's
Flask was authored by Armin Ronacher
Flask has three main dependancies:
* The routing
* Debugging
* Web Server Gateway Interface (WSGI) - a subsystem from Werkzeug
The template support is provided by Jinja2 with Command Line Integration from Click.
Extensions are used to get services such as for db access, web form validation, authentication etc.
The only requirement to start working with Flask is existence of Python in the system.
A virtual environment is a copy of the Python interpreter into which you can install packages privately, without affecting the global python interpreter in your system.
Venv's also prevent package clutter and version conflicts in the system's Python interpreter (keeps things neat and clean).
## Setting up a virtual environment
Venv's are natively supported in Python 3 (although those using Ubuntu, the standard package is not installed). 
``` sudo apt-get install python3-venv ``` This should set up venv in Ubuntu's Python3

The command below creates a virtual env:
``` python3 -m venv virtual-environment-name ```
where the desired name is passed as an argument

To create a virtual environment with the name venv inside a subdirectory called venv, the command below should work
``` python3 -m venv venv ```

In python2 (which lacks venv package), then a 3rd party utility (virtualenv) is used.
Installation of virtualenv (linux): ``` sudo pip install virtualenv ```
Installation of virtualenv (windows): ``` pip install virtualenv ```
Then create a virtual environment using: ``` virtualenv venv ```
## Working with a virtual environment
Activating in Linux: ``` source venv/bin/activate ```
Activating in Windows: ``` venv\Scripts\activate ```
Then the command prompt looks like ``` (venv) $ ```
Deactivating the terminal: ``` deactivate ```
## Installing python packages with pip
After activating the virtual environment, Flask can be installed by running:
``` pip install flask ```
To check the installed packages: ``` pip freeze ```

