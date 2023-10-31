# scilpypi

Scilpy building and PyPI publishing tools

## Preparing the environment

First, create a virtual environment and install the required packages:

```bash
virtualenv -p python3.10 venv
source venv/bin/activate

pip install -r requirements.txt
```

## Creating a new release

Releases on PyPi that target linux architectures have to abide either the `manylinux`
or the `musllinux` operating environment. To aleviate the need to setup any of those 
environments, we use a *Github worker* to do the job, configured with `ciBuildWheel`.

To create a new release, you need to create a new tag and push it to the remote repository:

```bash

git tag -a <tag> -m "<message>"
git push origin <tag>
```

Then, you need to create the release on Github and target the tag you just created. This 
will trigger the release building `workflow`, which will produce the source archive and 
the wheels for the different architectures.

Once the workflow is done, you can download the wheels and source archive from the
release page, or from the *Github Action*, in the `artifacts` of the build job.

## Publishing a new release

Once the release is built, you can publish it to PyPi using `twine`. Place the source 
archive and wheels you just downloaded in a directory called `dist`. Before pushing to 
the official PyPi repository, you can test the release by using the `testpypi` repository:

```bash

twine upload --repository testpypi dist/*
```

Then, when you're ready, publish the relase to PyPi:

```bash

twine upload dist/*
```
