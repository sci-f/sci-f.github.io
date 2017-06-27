# Hosting a Competition

**under development**

This guide will get you started to host your own containers-ftw competition. The general workflow is the following:

 1. You publish a competition via a Github repo, and PR to [containers-ftw](https://www.github.com/containers-ftw/containers-ftw.github.io) to post your competition (this page will be added soon). 
 2. Participants enter by way of submitting a pull request (PR) to your repo
 3. The PR is for a Singularity build file (a container) that the continuous integration will run and assess for the metric
 4. The participant(s) that best meet your metric(s) of success win the competition.


## Competition Materials
You must provide the following to your participants:

- **Data**, either hosted with the Github repo, or downloaded at runtime. The organization and instructions for your data will be packaged with the tempate that you choose with the [cftw](https://www.github.com/containers-ftw/containersftw) software. 
- **Instructions** should be provided with clear description of the dataset, the problem to solve, and the metric of assessment. The level of detail that you provide is dependent on your competition.

The base template to start your competition can be easily obtained with the [containers-ftw](https://www.github.com/containers-ftw/containersftw) command line software.


## Competition Metrics via Tests
The participant is going to be producing some output after his or her analysis that represents an answer to your query. In order to validate the correctness of the format, or assess the output based on a metric of your choosing, we use continuous integration. This is an optimal strategy because it will be run whenever someone forks your competition repo, and then submits a PR to add his or her result.

### Tests
You should write your tests in the `tests` folder inside the `analysis` folder, and then write as many test commands as you need in the `.travis.yml` file that is provided with your template. For language specific templates, example test commands are provided.


## Container Organization
The base template that is generated for Github is mapped into a Singularity container. We follow a basic organization that we believe is intuitive to find code and data in the containers. The mapping from host (Github base repo provided by [containers-ftw](https://www.github.com/containers-ftw/containersftw) to container looks like the following. Folders that are eventually bound to the host for generating and running analyses are marked with an *:

```
analysis           ---->          /code/analysis*                    # top level folder for analyses, mapped to host   
analysis/tests     ---->          /code/analysis/tests               # tests that are run (specified by `.travis.yml`)      
analysis/helpers   ---->          /code/analysis/helpers             # functions provided by you to get your competitors started

functions          ---->          /code/functions                    # functions for container operations, nothing to see here
data/input         ---->          /data/input                        # data served with the container, added during build
data/work          ---->          /data/work*                        # intended for intermediate data files and working
result             ---->          /result                            # top level for all results in the container
result/analysis    ---->          /result/analysis*                  # This is where the user is expected to write final output to
result/web         ---->          /result/web                        # If there is a web component of output
result/pub         ---->          /result/pub                        # written, publication type documents or other
```

Optionally, if you require the user to mount the data externally (for example if it's really big), you would specify the shell command to do this mapping:

```
data/mnt          --->            /data/mnt*
```

If you do not put data in here, no worries, even if the directory is mounted it's just mounting an empty directory.


### Code
The `/code` folder Is the base of code, and within it `/code/analysis` is where the user's final analysis script (`main.py`) will be copied, along with other functions that are provided by the creator. It looks something like this:

```
/code
    /analysis
        main.py
    /functions
```

If you have functions or helpers that you want to give the user, you should add them to the analysis folder, in whatever organization you think appropriate. In the example below, we have provided some utility functions that we give to the user in `main.py` to easily read in the data:

```
/code
    /analysis
        /helpers
           utils.py
        main.py
    /functions
```

The user has complete control over what happens in `main.py`, and can add any supplementary modules or files to be included. In your competition repo, the user writes his or her code to the folder analysis, and develops the image with this folder mounted. The only requirement is that it accepts the arguments as specified by the competition host.


### Data
The base folder `/data` is intended primarily for inputs and intermediate data products that result from the analysis. 

```
/data
   /mnt
   /input
   /work
```

Each of the folders has a specific use case, and the specific format of the data inside depends on the kind of dataset.

 - `/input` is where the user would expect to find data provided by the container. This is copied from the repository `/data/input` folder.
 - `/mnt` is where any external data would be mounted
 - `/work` is an intermediate location intended for working. It would be good practice for the user to mount work to write files, and `/code/analysis` for writing the scripts.


### Results
While a result could be considered data, it is fundamentally different in that it is intended to be digested by humans, and not computers. Thus, we take a template-based approach. The raw result file (for example, `.csv` or other) is intended to go in the `/result/analysis` folder. Outputs that are intended to be run with a web server go under web (which is linked to /var/www/html). Any publication or document-type results go under `pub/`

```
/result
   /analysis
   /web
   /pub
```

# Generate Your Base
We will flush this out in more detail as it is developed, but to start you would first install the [cftw](https://www.github.com/containers-ftw/containersftw) command line utility by installing from pip:

```
pip install cftw

# For the development version, install from Github:

pip install git+git://github.com/containers-ftw/cftw
```

The `cftw` software is now installed on your machine:

```
which cftw
/home/vanessa/anaconda3/bin/cftw
```

and we can see its usage:

```
cftw
usage: cftw [-h] [--version] [--debug] {init,add} ...

Containers For the Win Base Generator

optional arguments:
  -h, --help  show this help message and exit
  --version   show software version
  --debug     use verbose logging to debug.

commands:
  containers-ftw generation commands

  {init,add}  generation commands
For help with an action, type [action] --help
```

We would first want to generate a base with the `init` command:

```
cftw init
Please provide a base template with --template/-t. Available templates are: 
ubuntu16.04-python2
continuumio-anaconda3
-------------------------------------------------------------
For help with init, cftw init --help
```

It's telling us that it needs a template, and an output folder. Let's provide a template. First I am going to run it in a random location on my computer. The software is conservative in that it won't overwrite things in a non-empty directory:

```
cftw init --template ubuntu16.04-python2
ERROR Directory is not empty - will not risk overwrite!
Please make an empty folder and run cftw init again.
```

Instead, we should be more careful and start in an empty folder.

```
mkdir competition
cd competition
```

When we empty the folder, generation is ok!

```
cftw init --template ubuntu16.04-python2

DEBUG Starting base generation in /home/vanessa/Documents/Dropbox/Code/shub/ftw/containersftw/test
DEBUG Copying folder template ubuntu16.04-python2 to test
test/
    ubuntu16.04-python2/
        README.md
        .travis.yml
        Singularity

DEBUG Finished generation.
```

More to come soon! Still working on this.


# Participating in a Contest
The user should be able to build your provided container without a hitch - it would just be missing the `main.py` script with the participant's implemented analysis to produce some expected result. The instructions will vary slightly by container, but generally will look something like the following. First, the user will fork and clone the competition repo:

```
singularity create --size 2000 analysis.img
sudo singularity bootstrap analysis.img Singularity
```

And then connect to the container with shell and binding required directories

```
singularity shell -B analysis:/code/analysis -B /tmp:/data/mnt analysis.img
```
