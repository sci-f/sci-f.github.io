# Hosting Quick Start

This is the quick start guide to show you how to host your own containers-ftw competition. If you want a more detailed walkthrough, see the [hosting tutorial](hosting-tutorial.md). Note that both of these docs are heavily under development. The general workflow is the following:

 1. You publish a competition via a Github repo, and PR to [containers-ftw](https://www.github.com/containers-ftw/containers-ftw.github.io) to post your competition (this page will be added soon). 
 2. Participants enter by way of submitting a pull request (PR) to your repo
 3. The PR is for a Singularity build file (a container) that the continuous integration will run and assess for the metric
 4. The participant(s) that best meet your metric(s) of success win the competition.

## Step 1: Base Template
Install the [cftw](https://www.github.com/containers-ftw/containersftw) command line utility by installing from pip:

```
pip install cftw
pip install git+git://github.com/containers-ftw/cftw # development
```

The `cftw` software is now installed on your machine, and you can use it to select a competition base template.

```
which cftw
/home/vanessa/anaconda3/bin/cftw
cftw init --template continuumio-anaconda3
```

This will generate a basic file structure that you can add content to.

```
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
```


## Step 2: Labels and Metadata

You should use the `%labels` section of the Singularity build file to define metadata for your competition.

```
CONTAINERSFTW_TEMPLATE continuumio-anaconda3
CONTAINERSFTW_COMPETITION_HOST containersftw
CONTAINERSFTW_COMPETITION_NAME flavours-of-physics-ftw
```

In the `%environment` section you should define the bases for your data, results, and code, if they happen to be different from the default. For example, below we are providing data in the container, so we point the `CONTAINERSFTW_DATA` to be in `/data/input`.  If you are using a mount, this would be `/data/mnt`. If you are using both, then you can specify `/data`.

```
%environment
CONTAINERSFTW_DATA=/data/input
CONTAINERSFTW_RESULT=/code/results
CONTAINERSFTW_WORK=/code
export CONTAINERSFTW_DATA
export CONTAINERSFTW_RESULT
export CONTAINERSFTW_WORK
```

You should then write clear instructions for your participant in the `README.md` Minimally you should include:

 - Instructions to fork and clone the repo, and build the image (these are provided in the templates)
 - An overview for the background of the competition, the metrics/goals, and any relevant rules
 - A pointer to the user to build and then write code in the `main.py`.


## Step 3: Data
You have few options for where data can be obtained, and you can choose based on the size of the data.

  - **inside container**: data you want built into the container at `/data/input` can be put into `data/input`
  - **mapped to container**: data you want the user to (optionally download from somewhere) and mount you can instruct the user to add a bind to `/data/mnt` in the container from a location on the host. If you want the download to occur automatically for the user, you can have it done in the `%setup` section to the `$SINGULARITY_ROOTFS` 


## Step 4: Code

### Analysis 
The entire base of code will live in the analysis directory, mapped into the container at `/code` as a bind point to make it available on the participant's host machine. Within analysis, you should do the following:

  - **main.py**: is where the user will write their primary code. You should import example / base libraries, show loading data (ideally with helper functions) and how to run and save a result how you want it.
  - **metrics.py**: is a base of evaluation metrics and checks, to be used by the user and during continuous integration testing to evaluate a result.
  - **tests/**: is the testing folder, and you should write the tests necessary to first test for the existence and valid formatting of the result, and then submit the result to the competition portal (TBD).


### Helpers
The `analysis/helpers` folder is intended to house helpful functions for loading, finding, and saving data. It's up to you how much you want to provide, but minimally it's nice to give the participant the following:

 - **data.py**: functions for loading and listing datasets.
 - **results.py**: functions for saving data to filenames and locations where it must be for final testing
 - **logger.py**: an intuitive logger (`from logger import bot; bot.info("This is information!")` that you can show the user in the `main.py` and advise to add logging when needed.


This means that in the container, the user can `ls $CONTAINERSFTW_DATA` and see the data that is provided. Or save their output to `CONTAINERSFTW_RESULT` without thinking about what that actually maps to.


## Step 5 Tests
Once the user has forked, cloned, and added code to be evaluated by your metric, it needs to be tested. Testing comes down to what happens during continuous integration, and by default we run a set of python scripts in the `analysis/tests` folder that will simply look for output files generated in the result folder. 

**under development**

Finally, the test result is submit to the entry of the user in the competition, also by way of the cftw software (also under development).
