---
title: "Tutorial : Singularity and Docker"
date: 2019-09-20T11:38:57+01:00
categories: ["tutorial"]
tags: ["HPC","HTC","Singularity","Linux","container","docker","gitlab"]
language: en
---
## How to use private Gitlab Registry with Singularity

With Singularity release 3.3, it is now possible to push and pull a Singularity container into an OCI registry like Docker or Azure Container Registry. 
It also works with Gitlab registry, which are basically Docker registry. This is a great news for CI/CD with Singularity and Gitlab !

Here is how to proceed with a private Docker GitLab registry:

NB: *user* is the username gitlab, *passwd* the associated password, *container.sif* is the image singularity and *project* the name of the Gitlab project.

### Push: send a container to a registry

    singularity push --docker-username user --docker-password passwd container.sif oras://gitlab-registry/user/project:latest

### Pull: retrieve a container from a registry

    singularity pull --docker-username user --docker-password passwd container.sif oras://gitlab-registry/user/project:latest

It is also possible to use the environment variables SINGULARITY_DOCKER_USERNAME and SINGULARITY_DOCKER_PASSWORD to authenticate, or --docker-login interactive with singularity pull.

## Gitlab CI example

Here is a .gitlab-ci.yml example for build and push of a Singularity v3.4.0 container :

    image: 
        name: quay.io/singularity/singularity:v3.4.0
        entrypoint: [""]
    
    stages:
      - build
      - deploy
    
    # Build Singularity container
    singularity-image:
      stage: build
      script: 
         - singularity build container.sif Singularity
      artifacts:
        paths:
         - container.sif
    
    # Push the image on the registry
    deploy:
      stage: deploy
      script:
         - singularity push --docker-username "${CI_REGISTRY_USER}" --docker-password "${CI_REGISTRY_PASSWORD}" app.sif oras://"$CI_REGISTRY_IMAGE"/"$CI_PROJECT_NAME":"$CI_COMMIT_TAG"