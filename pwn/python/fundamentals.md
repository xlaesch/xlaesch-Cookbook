
Python is an interpreted language, meaning the code itself is not compiled into machine code, instead, it is interpreted by the Python program and the instructions in the script are executed. 

Python can be executed from `.py` files or running it directly inside the Python IDLE. Integrated Development and Learning Environment that exists directly in our terminal. 

Another method is based on adding the shebang `#!/usr/bin/env python3` in the first line of a python script. On Unix-based system, a pound sign and an excalamation mark causes the following command to be executed along with all of the specified arguments. So we can directly execute the program without entering `python` at the command-line.

# Coding Style

Python variable names follow the snake_case naming convention. All variable names should be all lower case initially and an underscore should separate any potential need for multiple word names. 

# Classes

A class is a spec of how an object of some type is produced. The result  of instantiating such a class is an object of the class.

Classes are defined using the `class` keyword, in the CapWords naming conventions. The `__init__` function is automatically called by Python once a new instance of a class is requested. The `self` parameter is a mandatory, first parameter of all class functions. Allows classes to refer to their own variables and functions. We can then refer to other functions within class functions by calling `self.other_func()` or `self.topping`.

## Magic Methods

Functions or methods which exist by default and have a default implementation in all classes. All classes inherit from a base class `object`. We may overwrite magic methods in the class definition.

# Libraries

A python file is also know as a module. A library is a collection of knowledge that we can borrow in our projects without reinventing the wheel.

```python
from datetime import datetime as dt

print(dt.now())
```

The default `site-packages`/`dist-packages` locations are the following:
- Windows 10: `PYTHON_INSTALL_DIR\Lib\site-packages
- Linux: `/usr/lib/PYTHON_VERSION/dist-packages/`

`site-packages` is a handled by `apt` on Debian-based systems.

We can tell Python to look in a different directory before searching through the site-packages directory by specifying `PYTHONPATH`.

```shell
PYTHONPATH=/tmp/ python3

# can be verified
>>> import sys
>>> sys.path
['', '/tmp/', '/usr/lib/python39.zip', '/usr/lib/python3.9', '/usr/lib/python3.9/lib-dynload', '/usr/local/lib/python3.9/dist-packages', '/usr/lib/python3/dist-packages', '/usr/lib/python3.9/dist-packages']
```
## Pip

"pip installs packages" responsible for installing external packages. 

```shell
# installating packages
pip install flask

# upgrading
pip install --upgrade flask

# installing python modules at target locatio
pip install --target /var/www/packages/ requests

# uninstalling
pip uninstall [package]

# listing the installed packages
pip freeze
```

## Virtual Environments

The `venv` module allows us to create virtual environments for our projects, consisting of a folder structure for the project environment itself, a copy of the Python binary and files to configure our shell to work with this specific environment.

```shell
# vrtual environment called academy
python3 -m venv academy

# activate the script in academy/bin
```

