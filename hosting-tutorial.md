# Hosting a Competition Tutorial

This is the detailed guide and tutorial to show you how to host your own containers-ftw competition. Specifically, we will be converting the Kaggle Competition [mu tau tau tau!](https://www.kaggle.com/c/flavours-of-physics/data) into a [competitive container](https://github.com/containers-ftw/flavours-of-physics-ftw). If you want a shorter quickstart, see the primary [hosting](hosting.md). The general workflow is the following:

 1. You publish a competition via a Github repo, and PR to [containers-ftw](https://www.github.com/containers-ftw/containers-ftw.github.io) to post your competition (this page will be added soon). 
 2. Participants enter by way of submitting a pull request (PR) to your repo
 3. The PR is for a Singularity build file (a container) that the continuous integration will run and assess for the metric
 4. The participant(s) that best meet your metric(s) of success win the competition.


# Background

## Competition Materials
You must provide the following to your participants:

- **Data**, either hosted with the Github repo, or downloaded at runtime. The organization and instructions for your data will be packaged with the tempate that you choose with the [cftw](https://www.github.com/containers-ftw/containersftw) software. 
- **Instructions** should be provided with clear description of the dataset, the problem to solve, and the metric of assessment. The level of detail that you provide is dependent on your competition. For us this is easy, we will include the competition details in the README of the competition.

The base template to start your competition can be easily obtained with the [containers-ftw](https://www.github.com/containers-ftw/containersftw) command line software.


## Competition Metrics via Tests
The participant is going to be producing some output after his or her analysis that represents an answer to your query. In order to validate the correctness of the format, or assess the output based on a metric of your choosing, we use continuous integration. This is an optimal strategy because it will be run whenever someone forks your competition repo, and then submits a PR to add his or her result.


### Tests
You will write your tests in `analysis/tests`. These tests will be found automatically in the default templates' `.travis.yml` file that is provided, and you are free to add any additional tests that you might need. 


## Container Organization
The base template that is generated for Github is mapped into a Singularity container. We follow a basic organization that we believe is intuitive to find code and data in the containers. The mapping from host (Github base repo provided by [containers-ftw](https://www.github.com/containers-ftw/containersftw) to container looks like the following. Folders that are eventually bound to the host for generating and running analyses are marked with an *:

```
analysis                    ---->          /code/analysis*                    # top level folder for analyses, mapped to host   
analysis/tests              ---->          /code/analysis/tests               # tests that are run (specified by `.travis.yml`)      
analysis/helpers            ---->          /code/analysis/helpers             # functions provided by you to get your competitors started
analysis/results            ---->          /analysis/results*                 # This is the results base
analysis/results/web        ---->          /code/results/web                  # If there is a web component of output
analysis/results/pub        ---->          /code/results/pub                  # written, publication type documents or other
functions                   ---->          /code/functions                    # functions for container operations, nothing to see here
data/input                  ---->          /data/input                        # data served with the container, added during build
data/work                   ---->          /data/work*                        # intended for intermediate data files and working
```

Optionally, if you require the user to mount the data externally (for example if it's really big), you would specify the shell command to do this mapping:

```
data/mnt                    ---->            /data/mnt*
```

If you do not put data in here, no worries, even if the directory is mounted it's just mounting an empty directory.


### Code
The `/code` folder Is the base of code, and mapped from `/analysis` on the host. Within it you will find the user's final analysis script (`main.py`) will be copied, along with other functions that are provided by the creator. It looks something like this:

```
/code
    /helpers
    /results
    main.py
```

If you have functions or helpers that you want to give the user, you should add them to the analysis folder, in whatever organization you think appropriate. In the example below, we have provided some utility functions that we give to the user in `main.py` to easily read in the data:

```
/code
    /helpers
       utils.py
    /results
    main.py
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
While a result could be considered data, it is fundamentally different in that it is intended to be digested by humans, and not computers. We also want the results to be available to the host, and minimize the number of locations that need to be bound. It works naturally, then, that we include results in the analysis folder, and within the results folder we take a template-based approach. The raw result file (for example, `.csv` or other) is intended to go in the `/results/submission` folder. Outputs that are intended to be run with a web server go under web (which is linked to /var/www/html). Any publication or document-type results go under `pub/`

```
/results
   submission.csv
   /web
   /pub
```

# Generate your Competition

## Base Template
To start you should first install the [cftw](https://www.github.com/containers-ftw/containersftw) command line utility by installing from pip:

```
pip install cftw
pip install git+git://github.com/containers-ftw/cftw  # development
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

It's telling us that it needs a template, and an output folder. Let's provide a template. The template will be generated in a folder with the equivalent name in the present working directory. 
When we empty the folder, generation is ok!

```
cftw init --template continuumio-anaconda3

DEBUG Starting base generation in /home/vanessa/Documents/Dropbox/Code/shub/ftw/containersftw/test
DEBUG Copying folder template continuumio-anaconda3 to test

continuumio-anaconda3/
├── analysis
│   ├── helpers
│   │   └── README.md
│   ├── results
│   │   └── README.md
│   ├── README.md
│   └── tests
│       └── README.md
├── data
│   ├── input
│   │   └── README.md
│   └── mnt
│       └── README.md
├── README.md
└── Singularity

DEBUG Finished generation.
```

If you try to generate a template and the folder already exists, then you will get an error:

```
cftw init --template ubuntu16.04-python2

DEBUG Starting base generation in /home/vanessa/Documents/Dropbox/Code/shub/ftw/containersftw/test
DEBUG Copying folder template ubuntu16.04-python2 to test
ERROR Folder ubuntu16.04-python2 already exists, will not overwrite.
```

## Labels
I am first going to add some information to the `%labels` section of the Singularity build file, just to specify the name of my container competition.

```
CONTAINERSFTW_TEMPLATE continuumio-anaconda3
CONTAINERSFTW_COMPETITION_HOST containersftw
CONTAINERSFTW_COMPETITION_NAME flavours-of-physics-ftw
```

## Instructions
This is easy! The competition already has beautiful instructions and documentation, so we will provide a link to that that in the base README.md. Note that if there is no original documentation, you should briefly outline the background, goals, and metrics that you are using.


## Data
As we mentioned, we have a few options for where data can be obtained, and you can choose based on the size of the data. The labels of small, medium, and large are rather arbitrary, but based on the limits of Github, and the continous integration instance size.

  - **small data**: can live inside the container, meaning you can include in the repo and add in the %files section
  - **medium data** is data that is large to put in the container, but under 50MB so it can be served in a Github repo. This data you can include in the repo, and then bind to the container at runtime.
  - **large data** is data that can't live in the container, and can't live in a Github repo. For this data, you want to have it downloaded in the `.travis.yml` (meaning it will live in the continuous integration environment) and then cached. The user will also have to download it to his or her machine to test. In both cases, it is again bound to the container. We will talk through each of these use cases in detail.

### Small Data
For small data, you **could** download directly into the container during build, but we recommend that you add the data to your Github repository, and then add to the container in the `%files` section. If, for any reason, the download link doesn't work, you can rest assured that you have the data.  That would look like this:

```
%files
data/input/training.csv /data/input/training.csv
```

And importantly, since files are copied before the `%post` section, we make the directories we need to copy files to in the `%setup` section:


```
%setup

     # Data directories
     mkdir -p $SINGULARITY_ROOTFS/data/input   # data included with container
     mkdir -p $SINGULARITY_ROOTFS/data/work    # working directory
     mkdir -p $SINGULARITY_ROOTFS/data/mnt     # mounted datasets

     # Result directories
     mkdir -p $SINGULARITY_ROOTFS/code/results/submission
     mkdir -p $SINGULARITY_ROOTFS/code/results/web
     mkdir -p $SINGULARITY_ROOTFS/code/results/pub

     # Working code directories
     mkdir -p $SINGULARITY_ROOTFS/code

     /bin/bash functions/download_data.sh

```

### Medium Data
Medium data is too large to put in a container, and can't live on Github, meaning we need to download it to the host and then mount it to the container at runtime. This is the approach that we took for our Mu tau tau analysis, and specifically, we added a folder with helper [functions](https://github.com/containers-ftw/flavours-of-physics-ftw/blob/master/functions/download_data.sh) to the repo base that downloaded the data in the `%setup` section, which has access to the host filesystem (relative paths) **and** the container base at `$SINGULARITY_ROOTFS` (see the last line of the snippet above).

### Big Data
Big data is probably not optimal for a setup that uses Github and Continuous Integration, but given that this framework is moved to a larger cluster resource, you would handle big data by mounting it at runtime to the container, like this:

```
singularity run -B /scratch/bigdata:/data/mnt container.ftw
```

Note that we bind to the location `/data/mnt` in the container, in the case that we also have smaller data to include in `/data/input`


## Code
Code comes down to metrics, a main runscript, helpers, and tests.

### Metrics
Since this is a pre-existing competition, we are lucky that the evaluation metrics have been well thought out **and** implemented! This, we will take the same approach as we did with data - adding the files to our repo, and then they will be added to the container automatically when the folder where they live on the host is bound to the container. For example, I added these [evaluation metrics](https://raw.githubusercontent.com/yandexdataschool/flavours-of-physics-start/master/evaluation.py) to an extended [metrics](https://github.com/containers-ftw/flavours-of-physics-ftw/blob/master/analysis/metrics.py) module to drive checks and metrics for the competition.

### Runscript
Next I am going to add a `main.py`, and I will base this on the [baseline example](https://github.com/yandexdataschool/flavours-of-physics-start/blob/master/baseline.ipynb). If you look at the `Singularity` build recipe you will notice this file is what gets executed when someone runs the container:

```
%runscript

     # This will generate the scientific result!
     /opt/conda/bin/python /code/analysis/main.py
```

which again (if you aren't familiar with Singularity) would look like this:

```
singularity run -B /scratch/bigdata:/data/mnt container.ftw
```

### Helpers
The one difference will be that instead of having the users load and save data via their own functions, I'm going to write them helpers. In the `main.py` that I provide, I'll include all the code ready to go that imports these functions and loads data. This makes life easier for the competitors, but also gives you more control over how and where the data is loaded and saved. You can see the final helpers functions that I derived in [this folder](https://github.com/containers-ftw/flavours-of-physics-ftw/tree/master/analysis/helpers), and thus the user can interact with them in `main.py` as something like:

```
from helpers.data import load_data
train = load_data('train')

....

submission = save_result(result)
```

Generally, you want to provide enough structure to conceal (potentially) annoying things like knowing about the delimiters of your data to load, or whether to include columns or not. Whatever your strategy is, make sure that if you don't have any helper functions, your `main.py` has examples for how the user can find and load data.


### Tests
Finally, if you look at the `.travis.yml`, the tests are located in `analysis/tests`, and include basic functions to look if the result file exists, test that it loads, and validate fields. This should be where you score it in some way, and **TBD** the result will be uploaded to the score board.


## Environment
I want to make it really easy for my competitors to find things! Meaning, they should be able to locate everything they need via environment variables. Specifically, we need to tell them our choices for data, results, and work. We do that like this:

```
%environment
CONTAINERSFTW_DATA=/data/input
CONTAINERSFTW_RESULT=/code/analysis/results/submission
CONTAINERSFTW_WORK=/code/analysis
export CONTAINERSFTW_DATA
export CONTAINERSFTW_RESULT
export CONTAINERSFTW_WORK
```

This means that in the container, the user can `ls $CONTAINERSFTW_DATA` and see the data that is provided. Or save their output to `CONTAINERSFTW_RESULT` without thinking about what that actually maps to. If I wanted to ensure that my users write a result to a particular folder type, I could enforce that here.


# Participating in a Contest
The user should be able to build your provided container without a hitch - it should come down to building, shelling with data and analysis bound to it, and writing some additoinal code in the `main.py` script (the participant's implemented analysis). Then the container he or she produces can be run to produce some expected result in the result(s) files you are expecting. The instructions will vary slightly by container, but generally will look something like the following. First, the user will fork and clone the competition repo:

```
git clone https://www.github.com/vsoch/flavours-of-physics-ftw
cd flavours-of-physics-ftw
```

and then build an image:

```
singularity create --size 2000 analysis.img
sudo singularity bootstrap analysis.img Singularity
```

And then connect to the container, with shell and binding required directories. The minimal suggested binds is to bind the analysis folder so the user can edit files on their local machine and see them in the container.

```
singularity shell -B data/input:/data/input -B analysis:/code --pwd /code container.ftw
```
