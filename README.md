# Overview

This lab is an excercise in CICD concepts.

Before we proceed with the exercises, you'll need to install the lab requirements.

See lab [requirements.md](requirements.md).

# What is CICD?

From _u/spruce-bruce_ over at [(5) ELI5: What is CI/CD and Why do we need them? : devops](https://www.reddit.com/r/devops/comments/t5nufe/eli5_what_is_cicd_and_why_do_we_need_them/):

> Continuous integration is the practice of regularly integrating (merging) code with the rest of the organization. 

	It used to be common for individuals or teams to keep their code isolated in branches for many months and merge infrequently.
	
	Continuous delivery is a philosophy and set of practices around always keeping your application in a deployable state. 
	
	In order to achieve this we construct a deployment pipeline that serves to validate the correctness of changes, 
	and deliver those changes through a series of test environments, culminating in a production deploy.
	
	CI/CD together is the practice of merging changes frequently as devs work and having those changes pass through a series of automated tests. 
	
	Upon completion those changes are bundled into a release candidate that can then be deployed automatically into production. 
	
	Teams practicing CI/CD will typically produce many release candidates in one day.

# Exercises

- CI exercises: [ci.md](exercises/ci.md)
- CD exercises: [cd.md](exercises/cd.md)

# Review

In this lab, we covered the following:

- Installing a localalized Kubernetes cluster using [kind]()
- Installing [argo-workflows](https://argoproj.github.io/argo-workflows/), 
  [argo cli](https://github.com/argoproj/argo-workflows/releases/), [helm](https://helm.sh/docs/intro/install/), [minio](https://min.io/) 
- How to create a basic workflow with single and multiple, ordered steps using DAGs
- How to utilize the power of Docker-In-Docker in your workflows

I hope this lab has served as an easy-to-follow introduction to ci-cd concepts.
