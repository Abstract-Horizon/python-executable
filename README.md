# Python Executable

Simple script and recipe how to quickly make linux executable out of python project (where python is already installed)


## Quickstart

Copy `package-project.py` file to your python project root. Amend it to suite your needs/project structure.
See for defaults below.

When you want to package it - just run:

```shell script
python3 package-project.py
```

from root of your project (or anywhere really) and result will be in `target/your-executable`. It will contain all *but* python3
interpreter. You can run it with:

```shell script
./your-executable <parameters to be passed to your python code>
```


## Target Platforms

This is targeted to platforms where `bash` shell is installed. Originally it was made for Raspberry Pi
(actually simple ARM Debian distro) and OSX, but it is not limited to them.


## What Is It?

This was inspired with couple of decades or more old trick which I learnt with Java: jar files are
actually zip files. Zip files, due their design can be prepended with any arbitrary content and still
being valid zip files (due to reference to table of contents/directory is actually at the end of the file).
So, with Java you can always add following to the beginning of zip file:

```shell script
java -jar $0
```

If you rename it with dropping `.jar` and add executable flag (`chmod +x my.jar`) it will loook as any
other command but under the hood will execute script which will run your 'executable jar'.
You can package your code in some kind of 'uber-jar' to satisfy external dependencies.

This script creates pretty much the same thing:
- it installs all your python requirements from `requirements.txt` (modify if it is called differently) to some
  temp directory)
- does optionally some light templating (by adding timestamp after version defined in VERSION file)
- zips up all your source directories (configurable) along with requirements and version
- prepends shell (bash) script to zip file renaming all to name you put in the package script
- adds executable flag to resulting file (found in `target/<your-executable>`)


## Defaults

Script suggests some defaults but does not insist on them. _build script_ below is refering to
`package-project.py` python file (the build script).


### Directory Structure

By default it expects to have following directory structure:

```shell script
/src
/src/python  <- your code can live here
/src/resources <- non-python files can live here
requirements.txt  <- your project's requirements files
VERSION <- simple file with some version like 1.0
```

If you want to change it just update `SOURCE_PATHS` list at the top of the build script.


### Requirements

The build script expects to have `requirements.txt` (see above) in same directory. That file can be
empty. If you want to change the name (or path) change `REQUIREMENTS_FILE` variable in the build script.
Comment out or remove `install_requirements(REQUIREMENTS_FILE, target_directory=TARGET_REQUIREMENTS_PATH)`
line near the end of the build script if you dont' need it.


### Version

The build script provides way of adding version (with build timestamp) to the resulting zip file.
There's file called 'VERSION' with content `1.0`. The build script takes that file and appends
current date and timestamp to it while creating resulting file. Date and timestamp is in
`YYYYMMDDHHmmss` format. If you want to change it see `update_build_version` method.
If you don't want it, just comment out or remove `update_build_version(VERSION_FILE, TARGET_TEMPLATES_PATH)` line
near the end of the build script. At the same time you can comment out or remove `VERSION_FILE` variable
(near the top of the build script).


## Further Ideas

If you want to access files from, for instance, src/resource directory, you can always use
`local_resources` project (see here https://pypi.org/project/local-resources/0.0.1/). It will happily
provide you with binary file-like object for all files packaged in this zip file.
