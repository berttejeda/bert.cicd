# Exercise 1 - Submit the hello-world workflow

This is a proverbial _hello world_ example

1. Submit the workflow: `argo -n argo-cicd submit cicd/workflows/hello-world.yaml --watch`<br />
   Your terminal will refresh a few times before the workflow completes.
1. List workflows in the argo-cicd namespace: `kubectl -n argo-cicd get workflows`
1. Review the output of the latest workflow: `argo -n argo-cicd logs @latest`<br />
   You can also review the output of any given workflow by specifying its workflow name instead of `@latest`
1. Clean up workflows: `kubectl -n argo-cicd delete workflows --all`

# Exercise 2 - Directed acyclic graph 01

This exercise exercise demonstrates ordered execution of tasks

A [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) is a type of 
dependency graph that can not result in an infinite dependency loop.

1. Submit the workflow: `argo -n argo-cicd submit cicd/workflows/dag-01.yaml --watch`<br />
   Your terminal will refresh a few times before the workflow completes.
1. List workflows in the argo-cicd namespace: `kubectl -n argo-cicd get workflows`
1. Review the output of the latest workflow: `argo -n argo-cicd logs @latest`
1. We can review the dependency graph for this workflow from the argo-workflow UI: http://ci.example.local

# Exercise 2 - Directed acyclic graph 02

This exercise exercise demonstrates execution order of asyncronous tasks

1. Submit the workflow: `argo -n argo-cicd submit cicd/workflows/dag-02.yaml --watch`<br />
   Your terminal will refresh a few times before the workflow completes.
1. List workflows in the argo-cicd namespace: `kubectl -n argo-cicd get workflows`
1. Review the output of the latest workflow: `argo -n argo-cicd logs @latest`<br />
   Notice how the step named 'Third' finished executing before the step named 'Second'.<br />
   This is because there were no explicit dependencies defined between them.
1. We can review the dependency graph for this workflow from the argo-workflow UI
1. Create a port forwarding rule to your argo-workflow UI via the Kubernetes Port Forwarding feature: <br />
   `kubectl -n argo-cicd port-forward deployment/argo-workflows-server --address 0.0.0.0 2746:2746`
1. Navigate to the argo-workflows UI: http://127.0.0.1:2746
1. Click _Workflows_ in the lefthand navigation bar
1. Enter in _argo-cicd_ for the namespace
1. Click the workflow entry matching _dag-hello-02_
1. Review the dependency graph

# Exercise 3 - Docker-In-Dockr (DIND)

In this exercise, we are utilizing DIND to interact with the local docker daemon

1. Submit the workflow: `argo -n argo-cicd submit cicd/workflows/dind.yaml --watch`<br />
   Your terminal will refresh a few times before the workflow completes.
1. List workflows in the argo-cicd namespace: `kubectl -n argo-cicd get workflows`
1. Review the output of the latest workflow: `argo -n argo-cicd logs @latest`<br />
   Notice how we ran a docker container and showed its OS information<br />
   Pretty cool that we can directly interact with the docker daemon.

# Exercise 4 - Full Blown CICD

In this exercise we run through a complete CICD example where we:

- Checkout a git repo
- Build the docker image as per Dockerfile spec in the repo
- Deploy it our local Kubernetes cluster
- Verify deployment is up

As follows:

1. Generate/obtain a personal access token (PAT) from your docker account, see https://hub.docker.com/settings/security
1. Create the git credentials secret like so:<br />
   `kubectl create secret generic ssh-git-creds --from-file=ssh_privatekey=$HOME/.ssh/id_rsa --from-file=known_hosts=$HOME/.ssh/known_hosts`<br />
   Note that the corresponding ssh public key should have already been added to your github account
1. Create the docker config secret:<br />
   `export DOCKER_USERNAME=$(read -s;echo $REPLY)`<br /> # Here you enter in your docker username
   `export DOCKER_TOKEN=$(read -s;echo $REPLY)`<br /> # Here you enter in your docker PAT
   `kubectl -n argo-cicd create secret generic docker-config --from-literal="config.json={\"auths\": {\"https://index.docker.io/v1/\": {\"auth\": \"$(echo -n $DOCKER_USERNAME:$DOCKER_TOKEN|base64)\"}}}"`<br />
1. Submit the workflow:<br />
   ```
   argo \
   --namespace argo-cicd \
    submit cicd/workflows/cicd.yaml \
    --parameter "app=YOUR_APP_NAME" \
    --parameter "docker_repo=YOUR_DOCKER_REPO_NAME" \
    --parameter "repo=git@github.com:YOUR_USERNAME/YOUR_GIT_REPO.git" \
    --parameter "branch=YOUR_GIT_BRANCH_NAME" \
    --parameter "path=PATH_TO_DOCKERFILE_IN_YOUR_REPO" \
    --watch
    ```

For Example:

```
argo \
--namespace argo-cicd \
submit cicd/workflows/cicd.yaml \
--parameter "app=busybox" \
--parameter "docker_repo=tomtester" \
--parameter "repo=git@github.com:tomtester/busybox.git" \
--parameter "branch=main" \
--parameter "path=docker" \
--watch
```

Where your git@github.com:tomtester/busybox.git would be a github repo with the following structure:<br />

```
./README.md
./docker/Dockerfile
```

# Readings

- [Directed acyclic graph - Wikipedia](https://en.wikipedia.org/wiki/Directed_acyclic_graph)
- [Network Graphs for Dependency Resolution | by Patrick Coffey | Towards Data Science](https://towardsdatascience.com/network-graphs-for-dependency-resolution-5327cffe650f)
- [Core Concepts - Argo Workflows - The workflow engine for Kubernetes](https://argoproj.github.io/argo-workflows/workflow-concepts/)
