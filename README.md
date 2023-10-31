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

## Overwriting a release

If you need to overwrite a release, you need to delete the existing release and 
associated tag on `scilpypi` repository. Then, increment the **version extra** in
`version.py.fix` to the next `postX` available, starting from `post1`. Finally, 
start the build as described in the *Creating a new release* section, and publish.

The `postX` mechanism is used to avoid having to delete the release on PyPi, which
is not possible. If a PyPi release must be avoided at all costs, consider `yanking` 
the release instead. It won't prevent it from being installed using a strict version 
specifier, but will prevent its usage otherwise.

## Building a new release by hand

### Packaging the source

The source build is straightforward. First, install `pipx` :

```bash
pip install pipx
```

Then, trigger the source build :

```bash
pipx run -m build --sdist --outdir sdist/ <path to scilpy>
```

### Building the wheels

Though not recommanded, you can build a new release on your local machine using 
ciBuildWheel. First, install the following pre-requisites:

- [Docker](https://docs.docker.com/get-docker/) : ciBuildWheel uses pre-generated 
    docker images as build environment for the different architectures.

Install `ciBuildWheel` in your environment:

```bash
pip install cibuildwheel
```

The configuration for the wheel build is located in `cibuildwheel.toml`. You can
trigger the build using the following command:

```bash
cibuildwheel  <path to scilpy> \
    --config-file <path to scilpypi>/cibuildwheel.toml \
    --output-dir wheelhouse/
```
