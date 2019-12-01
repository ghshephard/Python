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
venv doesn't do everything that virtualenv can do, and pipenv is a bigger beast)

Pip can be installed with:

* sudo apt update
* sudo apt install python3-pip


virtualenv and virtualenvwrapper can be installed with:

* pip3 install virtualenv
* pip3 install virtualenvwrapper

Outside of virtualenv/virtualenvwrapper, Nothing should typically ever be pip installed in the base environment.   

A good way of enforcing this is:

* export PIP_REQUIRE_VIRTUALENV=true

in your .bashrc.


VirtualEnvWrapper can be started with:

* mkdir ~/.virtualenvs

And, in .bashrc: 

    export WORKON_HOME=~/.virtualenvs
	source {dir_where_virtual_env_wrapper_was_installed}/virtualenvwrapper.sh

Note - that installing virtualenvwrapper with apt-get places virtualenvwrapper in /usr/share
instead of /usr/local/bin.  Typically virtualenv/virtualenvwrapper are installed with pip.
pip3, as of at least version 9.0.1, installs in /home/{username}/.local/bin.  Example:
    
    /home/shephard/.local/bin/virtualenvwrapper.sh

So, in that case, you will need to:
    
    source /home/shephard/.local/bin/virtualenvwrapper.shy

Also, once you have files located in those directories, you will want to source
your .profie to get $HOME/.local/bin into your path:

```
    # set PATH so it includes user's private bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
````

One challenge is the infamous:

```/usr/bin/python3.8: Error while finding module specification for 
'virtualenvwrapper.hook_loader' (ModuleNotFoundError: No module named 'virtualenvwrapper')
virtualenvwrapper.sh: There was a problem running the initialization hooks.

If Python could not import the module virtualenvwrapper.hook_loader,
check that virtualenvwrapper has been installed for
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.8 and that PATH is
set properly.```

https://medium.com/@gitudaniel/installing-virtualenvwrapper-for-python3-ad3dfea7c717
is a good writeup on that.   You need to ensure that there is a virtualenvwrapper 
installed in the version of python that you used when pip installing it.  Typically
it uses /usr/bin/python3 when you pip3 install, so the the following will work:

```# VirtualEnvWrappers are awesome for python
if [ -f /usr/local/bin/virtualenvwrapper.sh ]; then
  export WORKON_HOME=~/.virtualenvs
        export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
  source /usr/local/bin/virtualenvwrapper.sh
fi```

And everything resolved itself. 

Source .bashrc will get you:


```virtualenvwrapper.user_scripts creating /home/shephard/.virtualenvs/premkproject
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

If all goes will you will see:

```shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ mkvirtualenv project
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

```(project) shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ pip3 list
Package    Version
---------- -------
pip        19.3.1
setuptools 42.0.2
wheel      0.33.6
```

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


Adding SSL:
    cd ~/.jupyter
    openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
    vi .jupyter/jupyter_notebook_config.py
    c.NotebookApp.certfile = '/home/shephard/.jupyter/mycert.pem'     
    c.NotebookApp.keyfile = '/home/shephard/.jupyter/mykey.key'


You should now be able to access your environment at:
    https://foo.org:9936

# Other versions of Python:

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


From there, you add jupyter-lab (which pulls in all it's pre-requistes):

    pip3 install jupyterlab
    ipython kernel install --user --name "Python3.8"


Once you've added the kernels you want, you can verify they are there with:
```(py37) shephard@ubuntu-s-1vcpu-1gb-nyc3-01:~$ jupyter kernelspec list
Available kernels:
  python3.6    /home/shephard/.local/share/jupyter/kernels/python3.6
  python3.7    /home/shephard/.local/share/jupyter/kernels/python3.7
  python3.8    /home/shephard/.local/share/jupyter/kernels/python3.8
  python3      /home/shephard/.virtualenvs/py37/share/jupyter/kernels/python3
```
            
.
