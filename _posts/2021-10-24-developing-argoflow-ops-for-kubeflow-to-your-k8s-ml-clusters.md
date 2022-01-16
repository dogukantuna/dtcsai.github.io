---
title: Developing Argoflow Operations on Kubeflow for your k8s ML clusters
date: 2021-10-24
permalink: /posts/2021/10/developing-argoflow-operations-on-kubeflow/
tags:
    - kubeflow
---

Out there, somewhere in the DevOps/GitOps ecosystem a lot of secret sauce (ok, I accept these are widely-known tools for sure) tools there especially for task orchestration: Airflow, Prefect, Luigi, Jenkins (in some way), Argo, Kubeflow, MLFlow welcomes you. All of these tools are can be used for develop tasks for machine learning systems, deep learning systems, or anything you need to develop. Without a doubt, these tools are useful for astonishing integrations, basically backstage tools.
One of them recently has become my main pick for artificial intelligence operations. Just a short time after having that habit on a daily basis, wrote the previous *opinion**.
So, just saying as an option: if you’re already deeply shifted *(or be preparing)* your MLOps environment to the Kubernetes ecosystem and would like to manage all of your processes as pods, define them in YAML instead of Python via using ArgoCD Workflows.

[Here](https://www.datarevenue.com/en-blog/airflow-vs-luigi-vs-argo-vs-mlflow-vs-kubeflow) is a really good comparison of all of these techies. That’s why I’ll not go to mention deeply, but, want to give basic exp of the main reason you want to use Argo upon your machine learning systems; want to run a wide variety of tasks written in different stacks, orchestrate common machine learning tasks such as experiment tracking, prediction, recom engine, classification, hyperparameter tuning, and model training/deployment et cetera.

Argo is built to orchestrate tasks like Kubeflow did this in ML field to the specific tasks. At this point, it is quite possible to use this leverage to your side and perform these tasks with Argoflow + Kubeflow. Both focusing and sharing the same point, k8s (Kubernetes). Except for that, independently you can use Argo or Kubeflow. I choose both certainly. MLOps? MLFlow is kind of a more specialized tool that doesn’t allow you to define arbitrary tasks or the dependencies between them.

- Define workflows where each step in the workflow is a container.
- Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
- Easily run compute-intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
- Run CI/CD pipelines natively on Kubernetes without configuring complex software development products.

The main reason of making this, to make it customization easy, and have it managed through a GitOps tool like ArgoCD.

Pre-reqs are: kubectl, yq, Python3.9, kubeseal
Python libs: bcrypt, passlib
Root files: kustomization.yaml, kubeflow.yaml

```console

# Installation ArgoCD on your instance.

ec2-user@ip-10.0.1.85:~$
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64

ec2-user@ip-10.0.1.85:~$
chmod +x /usr/local/bin/argocd

# For concrete versions of ArgoCD

VERSION=<TAG> # Specify desired TAG from https://github.com/argoproj/argo-cd/releases
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

Note that: Argo CD has to be able to access your repo to deploy applications. So, if you have an issue see the instructions provided by Argo CD documentation.

```console

# Public distribution credentials
ec2-user@ip-10.0.1.85:~$
kubectl apply -k distribution/argocd/base/

# Private distribution credentials
ec2-user@ip-10.0.1.85:~$
kubectl apply -k distribution/argocd/overlays/private-repo/

ec2-user@ip-10.0.1.85:~$
python3 -c ‘from passlib.hash import bcrypt; import getpass; print(bcrypt.using(rounds=12, ident=”2y”).hash(getpass.getpass()))’

```

After that initial installation of ArgoCD, firstly you have to fork this repository and move your clone your fork locally. Then, assign any customization you desire in the kustomize folders of the Kubeflow applications.

Finally, you’re able to follow Argo’s official recommended instructions for [credential management](https://github.com/argoflow/argoflow#installing-kubeflow)*, [ingress and certificate](https://github.com/argoflow/argoflow#ingress-and-certificate), and [application specs](https://github.com/argoflow/argoflow#customizing-the-jupyter-web-app).

Now, you’re free to deploy Kubeflow:

```console

ec2-user@ip-10.0.1.85:~$
kubectl apply -f argocd-applications/kubeflow-roles-namespaces.yaml

# To deploy everything specified in the root kustomization.yaml, execute:

ec2-user@ip-10.0.1.85:~$
kubectl apply -f kubeflow.yaml

```

Finally, your apps being deployed will appear in the ArgoCD UI and what the resources for each instance you already created.
Be free to serialize the resources of your ML systems via yet another deployment language.

###Extra: Deployment via emissary executor on GCP

Except for the flow above, also deploy a new Kubeflow Pipelines cluster on AI Platform with emissary executor.
AI Platform Pipelines makes it easier to get started with MLOps by saving you the difficulty of setting up Kubeflow Pipelines with TensorFlow Extended (TFX). Kubeflow Pipelines is an open source platform for running, monitoring, auditing, and managing ML pipelines on Kubernetes. TFX is an open source project for building ML pipelines that orchestrate end-to-end ML workflows.

- For AI Platform Pipelines, check the “Use emissary executor” checkbox during installation.
- For Kubeflow Pipelines Standalone, install env/platform-agnostic-emissary:

```console

gcp-user@ip-10.0.1.75:~$
kubectl apply -k “github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-emissary?ref=$PIPELINE_VERSION”

```

### Initial component

As an example, you can now train an XGBoost model in a notebook use to demonstrate your first task.

Or build a complete pipeline for you need as following these architecture:

- data ingestion, validation, transformation, training, and serving.

### Complete Features List of Argo Platform

- UI to visualize and manage Workflows
- Artifact support (S3, Artifactory, Alibaba Cloud OSS, HTTP, Git, GCS, raw)
- Workflow templating to store commonly used Workflows in the cluster
- Archiving Workflows after executing for later access
- Scheduled workflows using cron
- Server interface with REST API (HTTP and GRPC)
- Directed-AG or Steps based declaration of workflows
- Step level input & outputs (artifacts/parameters)
- Loops
- Parameterization
- Conditionals
- Timeouts (step & workflow level)
- Retry (step & workflow level)
- Resubmit (memoized)
- Suspend & Resume
- Cancellation
- K8s resource orchestration
- Exit Hooks (notifications, cleanup)
- Garbage collection of completed workflow
- Scheduling (affinity/tolerations/node selectors)
- Volumes (ephemeral/existing)
- Parallelism limits
- Daemoned steps
- DinD (docker-in-docker)
- Script steps
- Event emission
- Prometheus metrics
- Multiple executors
- Multiple pod and workflow garbage collection strategies
- Automatically calculated resource usage per step
- Java/Golang/Python SDKs
- Pod Disruption Budget support
- Single-sign on (OAuth2/OIDC)
- Webhook triggering
- CLI
- Out-of-the box and custom Prometheus metrics
- Windows container support
- Embedded widgets
- Multiplex log viewer

### Notes

Opinion*: Reference to my introduction and idea-sharing poorly postings that wrote on my here, in Medium.
Credential management*: Credential management, also referred to as a Credential Management System (CMS), is an established form of software that is used for issuing and managing credentials as part of public key infrastructure (PKI).

### Further

- [Argo workflows documentation: the workflow engine for Kubernetes](https://argoproj.github.io/argo-workflows/)
- [Artifact Repository Configuration (AWS S3, GCS, Minio)](https://github.com/argoproj/argo-workflows/blob/master/docs/configure-artifact-repository.md)
- [Argo CLI references](https://argoproj.github.io/argo-workflows/cli/argo/)
