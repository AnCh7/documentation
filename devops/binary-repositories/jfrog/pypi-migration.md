## PyPI repositories

> References:
> https://www.jfrog.com/confluence/display/JFROG/PyPI+Repositories
> https://pip.pypa.io/en/stable/user_guide/#config-file

Artifactory fully supports PyPI repositories. There are three types of repos:
- Local repository is physical, locally-managed repository into which you can deploy artifacts
- Remote repository serves as a caching proxy for a registry managed at a remote URL such as https://pypi.python.org
- Virtual Repository aggregates packages from both local and remote repositories and can be accessed from a single URL

### Resolving from Artifactory

#### PIP

To display code snippets you can use to configure `pip` and `setup.py` to use your PyPI repository, select the repository and then click **Set Me Up**:

- use your username and Identity Token as `USERNAME/PASSWORD` to access Artifactory.
- create pip config. This file contains configuration parameters per repository (aliases):

```shell
pip config list -v
touch ~/.pip/pip.conf
```

- and add the following to the pip config:

```yml
[global]
index-url = https://<USERNAME>:<PASSWORD>@artifactory.example.com/pypi-virtual/simple
```

- pip commands examples:

```shell
# install package
pip install boto3==1.21.1

# install with full repository URL
pip install boto3 --index-url https://artifactory.example.com/pypi-virtual

# if credentials are required they should be embedded in the URL
pip install boto3 --index-url https://<USERNAME>:<PASSWORD>@artifactory.example.com/pypi-virtual

# search
pip search --index-url https://<USERNAME>:<PASSWORD>@artifactory.example.com/pypi-virtual some-package-name

# example with ENV vars and --extra-index-url
pip search --extra-index-url https://${ARTIFACTORY_PYPI_USERNAME}:${ARTIFACTORY_PYPI_PASSWORD}@artifactory.example.com/pypi-virtual
```

#### requirements.txt

The index URL can be specified in the first line of the file, for example:

```shell
--index-url https://artifactory.example.com/pypi-virtual
boto3==1.21.1
wsgiref==0.1.2
```

#### Poetry

Access remote PyPi repositories (such as https://pypi.org) through an Artifactory remote repository which provides proxy and caching functionality.

1. Use your username and Identity Token as `ARTIFACTORY_PYPI_USERNAME/ARTIFACTORY_PYPI_PASSWORD` to access Artifactory and then set these environment variables:

```shell
export POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_USERNAME=${ARTIFACTORY_PYPI_USERNAME}
export POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_PASSWORD=${ARTIFACTORY_PYPI_PASSWORD}
```

2. Update `pyproject.toml` file:

```toml
[[tool.poetry.source]]
name = "example-resolve"
url = "https://artifactory.example.com/pypi-virtual"
default = true
```

3. Update `poetry.lock` file by running `poetry lock --no-update`

```toml
[package.source]
type = "legacy"
url = "https://artifactory.example.com/pypi-virtual"
reference = "example-resolve"
```

#### Drone

Add `ARTIFACTORY_PYPI_USERNAME` and `ARTIFACTORY_PYPI_PASSWORD` as global organization secrets.

Add to your `.drone.yml`:

```yml
- name: format/tests
  image: python:3.9.9
  environment:
    POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_USERNAME:
      from_secret: ARTIFACTORY_PYPI_USERNAME
    POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_PASSWORD:
      from_secret: ARTIFACTORY_PYPI_PASSWORD
  commands:
    - pip install poetry==1.1.13
    - poetry install
    - etc.
```

#### Dockerfile

```dockerfile
ARG ARTIFACTORY_PYPI_USERNAME
ARG ARTIFACTORY_PYPI_PASSWORD

ENV POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_USERNAME=$ARTIFACTORY_PYPI_USERNAME
ENV POETRY_HTTP_BASIC_EXAMPLE_RESOLVE_PASSWORD=$ARTIFACTORY_PYPI_PASSWORD
```


### Publishing to Artifactory

#### distutils or setuptools

To deploy packages using distutils or setuptools you need to add an Artifactory repository as an index server to the `.pypirc` file (usually located in home directory):

```toml
[distutils]
index-servers = 
	artifactory

[artifactory]
repository: https://artifactory.example.com/pypi-local
username: <USERNAME>
password: <PASSWORD>
```

After creating a `.pypirc` file and a `setup.py` script at the root of your project, you can upload your egg (tar.gz) packages as follows:

```bash
python setup.py sdist upload -r artifactory
```

#### Manual upload

PyPI packages can also be uploaded manually using the [Web UI](https://www.jfrog.com/confluence/display/JFROG/Deploying+Artifacts) or the [Artifactory REST API](https://www.jfrog.com/confluence/display/JFROG/Artifactory+REST+API). For Artifactory to handle those packages correctly as PyPI packages they must be uploaded with `pypi.name` and `pypi.version` properties.

#### Poetry

1. Use your username and Identity Token as `ARTIFACTORY_PYPI_USERNAME/ARTIFACTORY_PYPI_PASSWORD` to access Artifactory and then set these environment variables:

```shell
export POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_USERNAME=${ARTIFACTORY_PYPI_USERNAME}
export POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_PASSWORD=${ARTIFACTORY_PYPI_PASSWORD}
```

2. Update `pyproject.toml` file. Please note that `pypi/pypi-local` is used (not `pypi-virtual/simple`):

```toml
# Current poetry version has an issue with multiple sources and does not respect
# default=false and secondary=true settings. It is recommended to use drone for publishing packages.
[[tool.poetry.source]]
name = "example-deploy"
url = "https://artifactory.example.com/pypi-local"
default = false
secondary = true
```

#### Drone

Add `ARTIFACTORY_PYPI_USERNAME` and `ARTIFACTORY_PYPI_PASSWORD` as global organization secrets.

Add to your `.drone.yml`. Use poetry to publish packages:

```yml
- name: artifactory-poetry-publish
  image: python:3.9.9
  environment:
    POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_USERNAME:
      from_secret: ARTIFACTORY_PYPI_USERNAME
    POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_PASSWORD:
      from_secret: ARTIFACTORY_PYPI_PASSWORD
  commands:
    - pip install poetry==1.1.13
    - poetry config repositories.example-deploy https://artifactory.example.com/pypi-local
    - poetry publish --repository example-deploy --build
```

or use Drone's plugin:

```yml
- name: artifactory-pypi-publish
  image: plugins/pypi
  settings:
    username:
      from_secret: ARTIFACTORY_PYPI_USERNAME
    password:
      from_secret: ARTIFACTORY_PYPI_PASSWORD
    repository: https://artifactory.example.com/pypi-local
    distributions:
    - sdist
    - bdist_wheel
    setupfile: setup.py
```

#### Makefile

```shell
util.publish:
  # Generate Identity Token in Artifactory and use it as password,
  # then make sure these are set in the env when publishing from local machine.
  # export POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_USERNAME=${ARTIFACTORY_PYPI_USERNAME}
  # export POETRY_HTTP_BASIC_EXAMPLE_DEPLOY_PASSWORD=${ARTIFACTORY_PYPI_PASSWORD}
  poetry publish --repository example-deploy --build
```



### Migration from PyPI Cloud

1) Artifactory: create pypi repositories
   - create local repo
   - create remote repo (for proxy and caching functionality)
   - create virtual (aggregate multiple pypi repositories, single URL for access)
2) Create users:
   - Artifactory: create permissions (pypi repositories access only)
   - Artifactory: create users and groups (pypi repositories access only):
     - for CI\CD (Drone, Concourse, CircleCI, etc.)
     - other (Vault, Airflow, etc.)
   - PyPI Cloud: create user for migration scripts
   - Configure PIP
3. Run `pypicloudMigrator` to copy packages from pypicloud to artifactory

4. Update CI/CD pipelines
   - for example add secrets to Drone
     ```
     ARTIFACTORY_PYPI_PASSWORD
     ARTIFACTORY_PYPI_USERNAME
     ```
   
5. Rolling out:
   - Add Artifactory documentation for users: general information (Artifactory and XRay), PyPI usage in particular
   
   - Update Github repositories where old PyPI server was used:
     ```
     drone files
     dockerfiles
     poetry files
     requirements.txt
     etc.
     ```
     
   - Search for:
     - old PyPI URL
     - old CI\CD environment variables, for example: `PYPI_` 
     - PIP configuration files, for example: 
       ```
       [distutils]
       [pypi]
       index-servers
       index-url
       python setup.py
       ```
     - Poetry configuration files, for example:
       ```
       POETRY_HTTP_BASIC_
       poetry publish
       --repository
       ```
     
   - Announce migration
   
   - Create PRs
     ```
     gcb feature/artifactory && \
     gaa && \
     gcmsg "Artifactory migration" && \
     gpsup && \
     gh pr create --title "Artifactory migration" \
     --body "Migration from PyPI Cloud to Artifcatory. Please see more info here https://url-to-documentation"
     ```
   
6. Sunset pypicloud:
   - remove build steps, org secrets from Drone, Concourse, CircleCI, etc.
   - remove infrastructure resources: helm charts, terraform files, kubernetes manifests, etc.
   - run `pypicloudMigrator.groovy` for the last time
   - remove credentials for password managers, etc. 
   - remove databases
