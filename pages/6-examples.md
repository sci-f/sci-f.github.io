---
layout: default
title: Example Use Cases
pdf: true
permalink: /examples
toc: false
---

## Example Use Cases
SCIF is powerful in that it supports multiple general use cases for scientific and systems evaluation and high level introspection. These use cases broadly fall in the areas of providing modular software, systems and metric evaluation, and guided collaboration to answer a scientific question. For all use cases, the recommendation is to use a contained environment (e.g., Singularity) for maximum reproducibility of the work.


## Quick Examples
You might find SCIF useful if you:

### A Research Scientist
 - want to package multiple environments or software modules to publish alongside a paper. A SCIF app might coincide with a particular step in your pipeline, and the entire analysis is run with a few calls to scientific filesystem entrypoints. You can share the scientific filesystem recipe for others to create, or (better) share a reproducible container built with it (that you used to conduct your analyses).
 - want to provide modular tools for your lab or other scientists. A SCIF recipe or container with a filesystem could serve different environments for tools for your domain of interest.
 - want to easily expose different interactive environments. For example, SCIF could be used to expose the same python virtual environment, but exporting different variables to the environment to determine the machine learning backend to use.

### An Administrator
 - want to provide users with small apps that perform a function (for a host or container). We can provide this example in the context of tests. A SCIF app could be a test that perhaps has its own environment and executables, and is easily accessed by your users by interacting with the filesystem.
 - want to to assess metrics. For example, if any SCIF app can discover all the other installed apps (that perhaps are domain specific functions or processing steps) we can assess each for a metric of interst. Since the environments, raw files, and runscripts are available, they can be discovered and used as desired. A machine learning "metric" app might parse the others for features, run them to produce an output, and then parse the output.
 - want to easily add consistent applications to user (or provided) containers. In that a SCIF app can be added in a modular fashion to a container (or other) recipe, during a build time an administrator can easily add one or more helper modules to user recipes. An administrator can also offer apps for the user to select from to install.

### Developers
 - akin to using a module system, you could use SCIF to provide different entrypoints for software (coinciding with version, or even the same software with a different environment variable exported to determine runtime behavior.) A good example of this is using common machine learning libraries with different backends (e.g., keras, tensorflow, torch). An entrypoint `keras-python2` would run python with an environment variable triggering using keras as a backend, `keras-python3` would do the same with python3, and then `keras-python` would target the most recent.
 - you want to implement (functionally) the "same thing" in different ways, and assess differences. The simplest example is to imagine recording runtime metrics for a console print of "Hello World" in multiple langauges. Each variation or implementation is a SCIF app, and the common base is the host or container.

If you have more examples, please [add them!](https://www.github.com/sci-f/sci-f.github.io).



<div>
    <a href="/tutorials"><button class="previous-button btn btn-primary"><i class="fa fa-chevron-left"></i> </button></a>
    <a href="/community"><button class="next-button btn btn-primary"><i class="fa fa-chevron-right"></i> </button></a>
</div><br>
