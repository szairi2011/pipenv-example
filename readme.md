## Install pipenv
First, we install pipenv at the system level:

```sh
$ pip install pipenv
```

## Create a virtual environment
To create a virtual environment for the current project we simply run: 

```sh
$ pipenv shell --python 3.10
```
This will create a virtual environment using Python 3.10 specifically and activate it. By default, the virtual environment will be created under the system default folder.

We can also force the use of a virtual environment under the project folder. For this, we cane either set the env variable in the terminal window `PIPENV_VENV_IN_PROJECT=1` or create .venv folder under the project directory .venv will force use of that forlder even if the virtuel environment for the project was already created somewhere else before; pipenv will dynamically switch to use the local .venv instead --> great feature.

## Run a command installed under the virtualenv

```sh
$ pipenv run pip freeze > requirements.txt # Perform the pinning of all direct and transitive dependencies inside a requirements.txt file that could be used by pip later; if needed.
```

```sh
$ pipenv run flask --version # Finds the version of the locally installed Flask dependency
```

## Install a Dev only dependency
To install a dependency that will not be used at runtime, e.g. to unit test our project, run:

```sh
$ pipenv install pytest --dev
```





