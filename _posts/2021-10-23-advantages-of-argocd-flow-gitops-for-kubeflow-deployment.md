---
title: Advantages of ArgoCD for Kubeflow Deployment
date: 2021-10-23
permalink: /posts/2021/10/advantages-of-argocd-for-kubeflow-deployment/
tags: 
    - kubeflow
---

GitOps has a story to tell you about Kubeflow machine learning streamlines, workloads, or all the same to workflows. By the early 2010s, DevOps gained high attraction and because of IT needs it has evolved to cover the whole information technology ecosystem. Until today and from the future this IT ecosystem creating highly sophisticated DevOps technologies. Likewise, the same thing is happening now for GitOps, a paradigm shift I mean. GitOps has approximately here since when pioneered in 2017. Proved its value in a very good way as a truly powerful alternative to traditional CI/CD environments.
With the development of container technologies, came here to be permanent as some kinda a part of DevOps culture. GitOps is a fluent way to do k8s cluster management and application delivery at scale. A paradigm or a set of practices that drives developers to perform tasks that typically fall under the purview of IT operations. Please ask for Google. It will tell you everything about you need to know what is GitOps.
On the other side, there’s a GitOps technology here maybe you’ve worked with before or just heard of it called ‘Argo CD.’ Actually a quite popular, emerging tool on the radar. More than 60+ companies use this in their prod environments every day. Argo CD follows the GitOps pattern of using Git repos as the source of truth for defining the desired application state. Automates the deployment of the desired application states in the specified target environments or k8s existing manifests specified in:

- kustomize apps,
- helm charts,
- ksonnet apps,
- jsonnet files,
- dir of yaml/json manifest,
- either any custom conf management tool configured as a config management plugin

Application’s deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit. According to an official explanation of ArgoCD by their website:

*Argo CD is implemented as a kubernetes controller which continuously monitors running applications and compares the current, live state against the desired target state (as specified in the Git repo). A deployed application whose live state deviates from the target state is considered OutOfSync. Argo CD reports & visualizes the differences while providing facilities to automatically or manually sync the live state back to the desired target state. Any modifications made to the desired target state in the Git repo can be automatically applied and reflected in the specified target environments.*

All these features make ArgoCD a clear fit for the Kubeflow pipelines. Already, Kubeflow is enchanting its pipelines with Argo Workflows. Argo Workflow contains Kustomize manifests that point to the upstream manifest of each Kubeflow component and provides an easy way for people to change their deployment according to their needs. ArgoCD application manifests for each component will be used to deploy Kubeflow. The intended usage is for people to fork this repository, make their desired kustomizations, run a script to change the ArgoCD application specs to point to their fork of this repository, and finally apply a master ArgoCD application that will deploy all other applications. Deploying as minikube, kind, MicroK8s, VMs, or as a cloud instance, it doesn’t matter. Declares true simplicity.

More than that;

- Speeds up mean time to deployment, increase productivity.
- Focus code, not container deployment, leave behind.
- Thanks to Git’s capability to roll back and fork, you’ll gain stabilized and reproducible rollbacks.
- More security consensus
