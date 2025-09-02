---
title: "CI/CD with Singularity, Gitlab CI and Singularity Registry"
date: 2019-09-24T14:10:16+02:00
categories: ["tutorial"]
tags: ["HPC","HTC","Singularity","Linux","container"]
language: en
---

A singularity registry is a dockerhub-like interface for sharing containers. With the sregistry client you can push/pull singularity containers into a singularity registry (public or private).

The following tutorial use a docker image with sregistry and singularity 2.6 installed (sysmso/singularity-ci). 

sregistry-cli is available here : https://singularityhub.github.io/sregistry-cli/

## How to upload container to sregistry with Gitlab CI
 
Example of `.gitlab-ci.yml` to build and push a container named `container.simg`, based on a `Singularity` recipe file into a sregistry :
```
image: sysmso/singularity-ci
 
stages:
   - build
   - deploy
 
singularity-image:
   stage: build
   script:
      - singularity build container.simg Singularity
   artifacts:
     paths:
      - container.simg
 
 deploy:
  stage: deploy
   script:
   - source /sregistry_file
   - echo $SREGISTRY_FILE > ~/.sregistry
   - sregistry push container.simg --name apc/tp-singu:latest --tag tuto
 ```
You have to register 3 variables in Gitlab CI in order to use this method :
 
`SREGISTRY_HOSTNAME` : URL of the registry
 
`SREGISTRY_TOKEN` : Sregistry Token
 
`SREGISTRY_USERNAME` : username in the registry
 
For security purpose you may want to protect these variables in Gitlab.
