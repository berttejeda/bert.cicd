# Overview

This lab is an excercise in CICD concepts.

# Local Deployment

- Install [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
- Create your kubernetes cluster: `kind create cluster --name kind --config k8s-bootstrap/kind-config.yaml`
- Create the argocd namespace: `kubectl create namespace argocd`
- Install argocd to that namespace: `kubectl -n argocd apply -f cicd/install.yaml`<br />
  - Notes: 
  	- I downloaded the install api document from https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  	- Default username for argocd UI is `admin`
  	- Password can be obtained via `$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo)`<br />
  	  as per the documentation https://argo-cd.readthedocs.io/en/stable/getting_started/