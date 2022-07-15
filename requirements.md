# Install Lab Requirements

## Create your local Kubernetes Cluster

1. Install [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
1. Create your kubernetes cluster: `kind create cluster --name kind --config k8s-bootstrap/kind-config.yaml`
1. Create your kubernetes config: `kind get kubeconfig | tee ~/.kube/kind.yaml`
1. Declare your KUBECONFIG: `export KUBECONFIG=$(ls ~/.kube/*.yaml | tr '\n' ':')`
1. Listy our kubernetes contexts: `kubectl config get-contexts -o name`
1. Switch kubernetes context to the newly-created cluster, e.g. `kubectl config use-context kind-kind`

## Install Required cli Tools

1. Install [helm](https://helm.sh/docs/intro/install/)
1. Install the [argo] cli app
	1. Download the binary:	`curl -sLO https://github.com/argoproj/argo-workflows/releases/download/v3.3.8/argo-linux-amd64.gz`
	1. Unzip: `gunzip argo-linux-amd64.gz`
	1. Make binary executable: `chmod +x argo-linux-amd64`
	1. Move binary to a location in your path:	`sudo mv ./argo-linux-amd64 /usr/local/bin/argo`
	1. Test installation: `argo version`

## Create the argo-cicd kubernetes namespace

1. Create the namespace: `kubectl create namespace argo-cicd`

## Install Minio

1. Install minio in the argocd namespace: 
1. `helm repo add minio https://helm.min.io/`
1. Update your local chart index: `helm repo update`
1. `helm install minio minio/minio -n argo-cicd`
1. Default username is `minioadmin`
1. accesskey can be obtained via `echo $(kubectl -n argo-cicd get secret minio -o jsonpath="{.data.accesskey}" | base64 -d; echo)`<br />
1. secretkey can be obtained via `echo $(kubectl -n argo-cicd get secret minio -o jsonpath="{.data.secretkey}" | base64 -d; echo)`<br />
1. Create a port forwarding rule to your minio UI via the Kubernetes Port Forwarding feature: `kubectl -n argo-cicd port-forward deployment/minio --address 0.0.0.0 9000:9000`
1. Navigate to the UI: http://127.0.0.1:9000
1. Login using the credentials obtained from the above installation steps 
1. Create a new bucket named `argo-workflow-bucket`

Readings:

- [Use Port Forwarding to Access Applications in a Cluster | Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)


## Install Argo Workflow

1. Add the helm repository for argo: `helm repo add argo https://argoproj.github.io/argo-helm`
1. Install argo-workflow: `helm install argo-workflows argo/argo-workflows --set server.extraArgs='{--auth-mode=server}' -n argo-cicd`<br />
   Notice how I've passed in `server.extraArgs`. This removes the authentication requirement from the argo-workflows UI
1. Apply the workflow controller config map to have it use minio for its artifact repository: `kubectl -n argo-cicd apply -f cicd/configmap.yaml`

Readings:

- [Quick Start - Argo Workflows - The workflow engine for Kubernetes](https://argoproj.github.io/argo-workflows/quick-start/)