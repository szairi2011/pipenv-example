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

## Locking dependencies
Although pipenv would automatically update the pipfile.lock each time a change to the dependency graph occurs, we better run below command before committing the changes to Git

```sh
$ pipenv lock
```

## Install production from pinned dependencies
Assume we want to install a production envionment from the pinned versions, all we need is to run:

```sh
$ pipenv install --ignore-pipfile
```

This tells pipenv to ignore the Pipfile, and to use what's in Pipfile.lock instead.
This way, we make sure to install the exact environment we had when we ran $ pipenv lock, resulting in same sub-dependencies and all.

This way we don't have to pin the versions inside the requirements.txt as in $ pip freeze > requirements.txt, and then having to reinstall in prod using $ pip install -r requirements.txt. 

## Install from Pipfile for Dev updates
Pipfile intends to replace the requirements.txt file, while providing a similar mechanism using Pipfile.lock if we are after a deterministic build as in production. 
This way, we can solve few issues highlighted under `https://realpython.com/pipenv-guide/`
Now if other developers need to update the code, in this case they can run:

```sh
$ pipenv install --dev
```

This will update and install all the regular and dev dependencies and sub-dependencies versions if they are not pinned inside the Pipfile. 
After all we need to maintain a healthy code through its lifetime, and safety patches or new versions of dependencies are imminent to apply for our code to evolve. We just need to rely on Unit tests to avoid breaking our code.


## Pipenv dependency resolution
Pipenv uses a dependency resolution algorithm that is based on the following rules:
- If a dependency is pinned in the Pipfile, it will be installed at that version.
- If a dependency is not pinned, but is required by another dependency that is pinned, it will
be installed at the same version as the dependency that requires it.
- If a dependency is not pinned, but is required by another dependency that is not pinned, it
will be installed at the latest version that is compatible with all the pinned dependencies.

If a conflicting dependency graph is detected by pipenv during resolution, it will through an exception highlighting the root cause, a real bless. Pip was not able to do that; although there seems to be an ongoing pip feature to do the same not sure when.

## Resolving dependency conflicts
Pipenv provides a way to identify the parent dependency that results into the conflicting sub-dependencies. For this we can run:

```sh
$ pipenv graph --reverse
```
The reversed graph could be easier to detect the root cause parent dependency, leading to this conflict, and then solve it.

### Other Pipenv useful features

To browse the code for the an installed dependency module, e.g. flask, we can run below command and specify the editor to use in this case we choose to edit the flask code in vscode (i.e. EDITOR=code, where the inline env variable EDITOR is set to code)

```sh
$ EDITOR=code pipenv open flask
```

### Migrate a project to Pipenv
If you have a project that is already using pip and virtualenv, you can migrate it to Pip, using the followng cheatsheet hack:

  1. From a shell terminal e.g. GitBash on Windows, make sure that the project virtual env is activated

  2. Create a requierements.txt file without version pinning

    ```sh
    $ pip list --format=freeze | cut -d'=' -f1 > requirements.txt
    ```
  3. Run below command to create Pipfile from requirements.txt

    NB: The last time I had to open a standalone Gitbash terminal outside vscode in order to generate the Pipfile, otherwise I run into an error

    ```sh
    $ pip install
    ```
    This will create all unpinned dependencies including their sub-dependencies
  
  4. Identify the main dependencies

    ```sh
    $ pip graph
    ``` 
  
  5. Amend the Pipfile to remove the sub-dependencies, and keep only the main ones. Move Dev only dependencies to the dev section of the file

  6. Generate a Pipfile.lock by running $ pipenv lock

  Now, we should be all set to manage the project with pipenv