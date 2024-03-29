Multiple elements of the stack required to manage a python Jupyter Labs environment, they are:

* python
* venv
* virtualenv wrappers
* jupyter-lab/notebook
* ipykernel
* pip

Some notes:  (Everything below assuming Ubuntu 18.04 environment)


# Virtual Environments 
In order to keep thing sane, all python work should always be done in virtual environment.

In general, the easiest way to create, switch, remove virtual environments is with Virtual Environment Wrappers
https://virtualenvwrapper.readthedocs.io/en/latest/

VirtualEnvironmentWrappers rely on virtualenv https://virtualenv.pypa.io/en/latest/

(Note - there is also venv (Python3) and pipenv (which is a combination of requirements and enviroment management) 
venv doesn't do everything that virtualenv can do, and pipenv is a bigger beast.  Also note the conversation on HN Re: Pipenv: https://news.ycombinator.com/item?id=21781421)

Pip can be installed with:

    sudo apt update
    sudo apt install python3-pip


virtualenv and virtualenvwrapper can be installed with: [2] 

    pip3 install virtualenv.    ## Note - this is the beginning of a lot of confusion.  See Bottom for notes on virtualenvlocation
    pip3 install virtualenvwrapper.  

Outside of virtualenv/virtualenvwrapper, Nothing should typically ever be pip installed in the base environment.   

A good way of enforcing this is:

    export PIP_REQUIRE_VIRTUALENV=true

in your .bashrc.

If, for some reason you need to do something with pip in your root environment, you can temporarily:
    
     $ PIP_REQUIRE_VIRTUALENV=false pip list
     
VirtualEnvWrapper can be started with:

    mkdir ~/.virtualenvs

And, in .bashrc: 

```
    export WORKON_HOME=~/.virtualenvs
    source {dir_where_virtual_env_wrapper_was_installed}/virtualenvwrapper.sh
```

Note - that installing virtualenvwrapper with apt-get places virtualenvwrapper in /usr/share
instead of /usr/local/bin.  Typically virtualenv/virtualenvwrapper are installed with pip.
pip3, as of at least version 9.0.1, installs in /home/{username}/.local/bin.  Example:
    
    /home/shephard/.local/bin/virtualenvwrapper.sh

So, in that case, you will need to:
    
    source /home/shephard/.local/bin/virtualenvwrapper.sh

Also, once you have files located in those directories, you will want to source
your .profie to get $HOME/.local/bin into your path:

```
    # set PATH so it includes user's private bin if it exists
    if [ -d "$HOME/.local/bin" ] ; then
        PATH="$HOME/.local/bin:$PATH"
    fi
````

## Debugging virtualenvwrapper

One challenge is the infamous:

```
    /usr/bin/python3.8: Error while finding module specification for 
    'virtualenvwrapper.hook_loader' (ModuleNotFoundError: No module named 'virtualenvwrapper')
    virtualenvwrapper.sh: There was a problem running the initialization hooks.

    If Python could not import the module virtualenvwrapper.hook_loader,
    check that virtualenvwrapper has been installed for
    VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.8 and that PATH is
    set properly.
```


https://medium.com/@gitudaniel/installing-virtualenvwrapper-for-python3-ad3dfea7c717
is a good writeup on that.   You need to ensure that there is a virtualenvwrapper 
installed in the **version of python that you used when pip installing it.**  Typically
it uses /usr/bin/python3 when you pip3 install, so the the following will work:

```
if [ -x "$(command -v virtualenvwrapper.sh)" ]; then
        # Judgement call what order to look custom python3, system3 python3, system python2 is what I'm using here.  
        # Ubuntu 20.04 comes with Python3 in /usr/bin
        if [ -f /usr/local/bin/python3 ]; then
                export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
        elif [ -f /usr/bin/python3 ]; then
                export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
        elif [ -f /usr/bin/python ]; then
                export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
        fi
        #echo "Set VirtualEnvWrapper to ${VIRTUALENVWRAPPER_PYTHON}"
        source "$(command -v virtualenvwrapper.sh)"
        export WORKON_HOME=~/.virtualenvs
fi


```

And everything resolves itself. 

Source .bashrc will get you:


```
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/premkproject
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/postmkproject
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/initialize
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/premkvirtualenv
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/postmkvirtualenv
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/prermvirtualenv
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/postrmvirtualenv
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/predeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/postdeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/preactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/postactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/get_env_details
```

# Creating Virtual Environments

You should then be able to make your first virtual environment with:

    mkvirtualenv project

If all goes will you will see: [1] 

```
    shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ mkvirtualenv project
    Using base prefix '/usr'
    New python executable in /home/shephard/.virtualenvs/project/bin/python3
    Also creating executable in /home/shephard/.virtualenvs/project/bin/python
    Installing setuptools, pip, wheel...
    done.
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/project/bin/predeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/project/bin/postdeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/project/bin/preactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/project/bin/postactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/project/bin/get_env_details
```

And, you should have a minimal environment:

```
    (project) shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ pip3 list
    Package    Version
    ---------- -------
    pip        19.3.1
    setuptools 42.0.2
    wheel      0.33.6
```

Note, there is an *implicit* indication that you want the first python in your path, in this case /usr/bin/python, to be
used as the python that will be used in this virtual environment.  It would be much cleaner if you specifically stated
which python you wish to use in the virtualenv - we'll see this below.


# Installing Jupyter-Lab

You can the install jupyter with:

    pip3 install jupyterlab

To get remote access working:

    jupyter notebook --generate-config    
    jupyter notebook password


    vi ~/.jupyter/jupyter_notebook_config.py
    c.NotebookApp.ip = '*'
    c.NotebookApp.open_browser = False
    c.NotebookApp.port = 9936

## Adding SSL
```
    cd ~/.jupyter
    openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
    vi .jupyter/jupyter_notebook_config.py
    c.NotebookApp.certfile = '/home/shephard/.jupyter/mycert.pem'     
    c.NotebookApp.keyfile = '/home/shephard/.jupyter/mykey.key'
```

You should now be able to access your environment at:
    https://foo.org:9936

# Other versions of Python:

**NOTE** - Before experimenting with this - when you start Jupyter, it will be started in the context of whatever venv you are in - which means that the libraries you have will be pip installed in that context.  So, you need to make sure that you have your libraries properly installed for your python kernel *prior* to launching jupyter, as pip installing them after you've launched will not install them in the kernel venv you launched.  This is very confusing the first time you run into it, but makes sense.  pip doesn't know which jupyter kernel you are running, only which venv it is in.

From within your jupyter notebook, do a 
````
!pip list -v  | head
Package            Version Location                                                     Installer
------------------ ------- ------------------------------------------------------------ ---------
attrs              19.3.0  /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
backcall           0.1.0   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
bleach             3.1.0   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
decorator          4.4.1   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
defusedxml         0.6.0   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
entrypoints        0.3     /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
importlib-metadata 1.0.0   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
ipykernel          5.1.3   /home/shephard/.virtualenvs/py37/lib/python3.7/site-packages pip
...
````

To get a sense of where your current pip fils are being read from/installed to.

To install a new version of python, for older versions of ubuntu you will need to add the deadsnakes repo:
https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa


Add the Repo and update the packages available.

    sudo add-apt-repository ppa:deadsnakes/ppa
    sudo apt-get update

Install the vesions of python you are interested in:

    sudo apt-get install python3.7
    sudo apt-get install python3.8


Once you have your python(s), pip, virtualenv, and virtualenvwrappers installed, you can proceed to create 
a new virtual environment with:

    mkvirtualenv -p /usr/bin/python3.8  py38

If you've been apt-get install python, they will have been installed in /usr/bin, and you can 
select whichever version you want.  You should then get:

```
    Running virtualenv with interpreter /usr/bin/python3.8
    Already using interpreter /usr/bin/python3.8
    Using base prefix '/usr'
    New python executable in /home/shephard/.virtualenvs/py38/bin/python3.8
    Also creating executable in /home/shephard/.virtualenvs/py38/bin/python
    Installing setuptools, pip, wheel...
    done.
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py38/bin/predeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py38/bin/postdeactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py38/bin/preactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py38/bin/postactivate
    virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py38/bin/get_env_details
```

**NOTE**:
If you get a "ModuleNotFoundError: No module named 'distutils.cmd' - see https://askubuntu.com/a/1260519

Essentially:

```
    Debian has decided that distutils is not a core python package, so it is not included in the 
    last versions of debian and debian-based OSes. You should be able to do sudo apt install 
    python3-distutils and it should work.
```

**Note 2 (Python 3.10)**

if you get this error

    ModuleNotFoundError: No module named 'distutils.cmd'

you can try this

    sudo apt-get install python3.10-distutils
    
Likewise for python3.9
   
    sudo apt-get install python3.9-distutils

**Note 3 (Python 3.11)**
If you get an error of the form while installling ipykernel:
```
      psutil/_psutil_common.c:9:10: fatal error: Python.h: No such file or directory
          9 | #include <Python.h>
            |          ^~~~~~~~~~
      compilation terminated.
      error: command '/bin/x86_64-linux-gnu-gcc' failed with exit code 1
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
  ERROR: Failed building wheel for psutil
```

The following should clear it up:
```
   $ sudo apt install libpython3.11-dev
```

Once you have the new version of python installed and virtualenv created, you add jupyter-lab (which pulls in all it's pre-requistes):

    pip3 install jupyterlab
    ipython kernel install --user --name "Python3.8"

In order to get auto-complete to work with with Pandas, you need to do the following:

    ipython profile create

This creates the profile

    ~/.ipython/profile_default/ipython_config.py

Edit ipython_config.py to add autocompletion:
 
     c = get_config()
     c.Completer.use_jedi = False


Once you've added the kernels you want, you can verify they are there with:

```
    (py37) shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ jupyter kernelspec list
    Available kernels:
      python3.6    /home/shephard/.local/share/jupyter/kernels/python3.6
      python3.7    /home/shephard/.local/share/jupyter/kernels/python3.7
      python3.8    /home/shephard/.local/share/jupyter/kernels/python3.8
      python3      /home/shephard/.virtualenvs/py37/share/jupyter/kernels/python3
```

Helpful commands to check your python environment:
````
    import sys; sys.path
    import sys; sys.executable
````

When you run:

    import foo
    
Python will search all of the folders in your `sys.path` for those modules.  The sys.path is constructed based on what directory python is located in - if you are running a venv vesion, it will try and use the lib, and lib-dynload directories relative to that version, in addition to the pythonnn.zip file.   

As well, python will automatically import site, (unless you override with the `-S` option), which will add some additional directories such as site-packages and dist-packages.  See https://docs.python.org/3/library/site.html#site.getsitepackages for more details.

Likewise to check your jupyter environment
````
$ jupyter --paths
config:
    /home/shephard/.jupyter
    /usr/local/etc/jupyter
    /etc/jupyter
data:
    /home/shephard/.local/share/jupyter
    /usr/local/share/jupyter
    /usr/share/jupyter
runtime:
    /home/shephard/.local/share/jupyter/runtime
````

Locations of your kernelspec .json files can be found with:

````
$ jupyter kernelspec list --json
{
  "kernelspecs": {
    "python2": {
      "resource_dir": "/home/shephard/.local/share/jupyter/kernels/python2",
      "spec": {
        "argv": [
          "python",
          "-m",
          "ipykernel_launcher",
          "-f",
          "{connection_file}"
        ],
        "env": {},
        "display_name": "Python 2",
        "language": "python",
        "interrupt_mode": "signal",
        "metadata": {}
      }
    },
    "python3": {
      "resource_dir": "/home/shephard/.local/share/jupyter/kernels/python3",
      "spec": {
        "argv": [
    .... 
    
 ````
 

Now that you've installed the various python kernels, you can access them from the notebook on a per-notebook basis. 

Some references:

https://vsupalov.com/developing-with-python3-8-on-ubuntu-18-04/


[1] Interesting - there have been some changes to mkvirtualenv.  Ran this command this evening with mkvirtualenv 20.2.2 and got:
```
shephard@m1ubuntu:~$ mkvirtualenv py39                                               
created virtual environment CPython3.8.5.final.0-64 in 177ms                                                                                                               
  creator CPython3Posix(dest=/home/shephard/.virtualenvs/py39, clear=False, no_vcs_ignore=False, global=False)                                                             
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/shephard/.local/share/virtualenv)                           
    added seed packages: pip==20.3.1, setuptools==51.0.0, wheel==0.36.1                                                                                                    
  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator,XonshActivator                                                                
virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py39/bin/predeactivate                                                                                 
virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py39/bin/postdeactivate                                                                                
virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py39/bin/preactivate                                                                                   
virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py39/bin/postactivate                                                                                  
virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/py39/bin/get_env_details 
```


[2] This pip3 install will dump a ton of modules, files, libraries into the directory of a *particular* Python install.  If, you later run a different version of python, you'll get an error along the lines of: 

```/Library/Developer/CommandLineTools/usr/bin/python3: Error while finding module specification for 'virtualenvwrapper.hook_loader' (ModuleNotFoundError: No module named 'virtualenvwrapper')
virtualenvwrapper.sh: There was a problem running the initialization hooks.

If Python could not import the module virtualenvwrapper.hook_loader,
check that virtualenvwrapper has been installed for
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3 and that PATH is
set properly.
```

The error message honestly couldn't be clearer - and there are a couple fixes. 
1. You can simply install virtualenv/virtualenvwrapper in the version of Python you are running.
2. You can set the environment variable "VIRTUALENVWRAPPER_PYTHON" to *point* to the version of python you should be running, and make sure that version is in your path.
3. Some combination of the above 2.


Here, on a macOS system, that I was rebuilding (So, I had to tell it to ignore the PIP_REQUIRE_VIRTUALENV *so I could install virtualenv* (Irony)


```

$ egrep -B 3 -A 3 homebrew .bashrc
...
if [ -x "$(command -v virtualenvwrapper.sh)" ]; then
        # Kind of a judgement call what order to look - homebrew, custom python3, system3 python3, system python2 is my prference
	if [ -f /opt/homebrew/bin/python3 ]; then
		export VIRTUALENVWRAPPER_PYTHON=/opt/homebrew/bin/python3 
	elif [ -f /usr/local/bin/python3 ]; then
		export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3 
	elif [ -f /usr/bin/python3 ]; then
...    
    
$ PIP_REQUIRE_VIRTUALENV=false pip3 install virtualenv virtualenvwrapper
Collecting virtualenv
  Using cached virtualenv-20.4.2-py2.py3-none-any.whl (7.2 MB)
Collecting virtualenvwrapper
  Using cached virtualenvwrapper-4.8.4-py2.py3-none-any.whl
...
Installing collected packages: virtualenv, virtualenvwrapper
Successfully installed virtualenv-20.4.2 virtualenvwrapper-4.8.4

$ python3
Python 3.9.2 (default, Feb 24 2021, 05:06:40) 
>>> import virtualenv, virtualenvwrapper
>>> virtualenv.__path__
['/opt/homebrew/lib/python3.9/site-packages/virtualenv']
>>> virtualenvwrapper.__path__
_NamespacePath(['/opt/homebrew/Cellar/python@3.9/3.9.2_1/Frameworks/Python.framework/Versions/3.9/lib/python3.9/site-packages/virtualenvwrapper'])
>>> 
```
