---
layout: default
title: The Scientific Filesystem (Really) Quick Start
pdf: true
permalink: /tutorial-really-quick-start
toc: false
---

Same commands, but [more detail here](/tutorial-quick-start), or get quick start code on Github [here](https://github.com/sci-f/hello-world.scif).

## 1. Get containers with the same scientific filesystem

```
singularity pull --name scif-cli shub://vsoch/scif:scif
docker pull vanessa/scif:hw
```

## 2. View the scientific filesystem entrypoint
```
docker run vanessa/scif:hw
./scif-cli 
```

## 3. Discover Installed Apps
```
docker run vanessa/scif:hw apps
./scif-cli apps
```

## 4. Commands
### Help
```
docker run vanessa/scif:hw help hello-world-env
./scif-cli help hello-world-env
```
### Inspect
```
docker run vanessa/scif:hw inspect hello-world-env
./scif-cli inspect hello-world-env
```
### Run

```
docker run vanessa/scif:hw run hello-world-echo
./scif-cli run hello-world-echo
```

### Test

```
# Passing Test (test script returns 0 with no arguments)
docker run vanessa/scif:hw test hello-world-script
./scif-cli run hello-world-echo
echo $?

# Failing Test (test script returns argument as return code)
docker run vanessa/scif:hw test hello-world-script 255
./scif-cli run hello-world-echo 255
echo $?
```

### Execute
```
docker run vanessa/scif:hw exec hello-world-echo echo "Another hello!"
./scif-cli exec hello-world-echo echo "Another hello!"
```

### Execute command with environment variable $OMG
```
docker run vanessa/scif:hw exec hello-world-env echo [e]OMG
./scif-cli exec hello-world-env echo [e]OMG
```

### Interactive shell
```
./scif-cli shell
docker run -it vanessa/scif:hw shell
```

### Shell with application active
```
 ./scif-cli shell hello-world-env
docker run -it vanessa/scif:hw shell hello-world-env
```

### Python interactive client
```
./scif-cli pyshell
docker run -it vanessa/scif:hw pyshell
```
