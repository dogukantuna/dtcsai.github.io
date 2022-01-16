---
title:  Argo UI for visualizing and managing GitOps workflows via k8s
date: 2021-11-22
permalink: /posts/2021/11/argo-ui-for-visualizing-and-managing-gitops-workflows/
tag: 
  - gitops
---

Argo CD has a great web UI that provides a real-time view of your application activity or visualizes workflows quite usefully. That’s why I wanted to write a short demonstration with this blog post. You know, Argo CD is getting more attention day by day, rising talent. It has numerous features and functionalities, which I mentioned in the blog post below:

1. [Advantages of ArgoCD for Kubeflow Deployment](https://dtcsai.github.io/posts/2021/10/advantages-of-argocd-for-kubeflow-deployment/)
2. [Developing Argoflow Operations on Kubeflow for your k8s ML clusters](https://dtcsai.github.io/posts/2021/10/developing-argoflow-operations-on-kubeflow/)

When I look into Argo CD more closely, I’m assuming that “okay, couldn’t possibly have to find any more features” but I find more features. For example: image-updater. Anyway, I’ll no longer be amazed by the magic of Argo CD. Let’s back to our main subject, Argo CD Web UI.

Argo CD’s user interface really doing a fantastic job at visualizing the k8s pods hierarchically and is also good at assisting developers better understand how their applications are deployed in the clusters. So, if you are running an Argo CD-based GitOps operation, at some point, you will want to leverage this UI.

Here, at this point, it’s quite elegant to bring up UI by following steps. To get started, you will need a minikube regardless of which OS you’re using.

minikube will let our Kubernetes locally (including Windows, macOS, and Linux dists). It creates a single node cluster contained in a virtual machine. For our demonstration, this cluster lets you use k8s ops without requiring the time and resource-consuming installation of full-blown k8s.

## Installation

### Ubuntu Server

```bash
# Download minikube package
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb

# Install package
sudo dpkg -i minikube_latest_amd64.deb
```

### OSX

```bash
# Install minikube package
brew install minikube
```

If *which minikube* fails after installation via brew, you may have to remove the old minikube links and link the newly installed binary:

```bash
brew unlink minikube
brew link minikube
```

### 1. Check and run minikube profile.

```bash
minikube status
👉  To start a cluster, run: "minikube start"
```

### 2. Create namespace for Argo CD.

```bash
kubectl create namespace argocd
```

### 3. Install manifest that provides everything Argo CD needed.

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 4. Check components that you’ve created so far.

```bash
kubectl get pod -n argocd
```

### 5. Check running clusters.

```bash
kubectl get pod -n argocd -w
```

### 6. Check services that you’ve created so far.

```bash
kubectl get svc -n argocd
```

### 7. Redirect your application’s port to another port

You can API server using the http://localhost:8080 after port forwarding. Here svc is targetting our cluster, and -n argocd 8080:443 manifesting port forwarding.

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now, API server can then be accessed using the localhost:8080.

![](https://miro.medium.com/max/2000/1*_81drpnK9NV50GS_iWkNaA.png)

## Log In Credentials

Of course, we need login credentials to access our web UI. We will use “admin” as an initial username, and also we will generate auto-generated admin-secret and decode this base64 formatted password via the commands below.

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
echo <YOUR_GENERATED_PASSWORD> | base64 --decode
```

Now, we can enter our decoded password into the UI and log in to our system.

![](https://miro.medium.com/max/2000/1*9dVD-6M9pDEK85dHqTBwog.png)

Deployments that you can create using this interface, now just depend on your GitOps. But, if you are just learning Argo CD, you can use these examples in this official demonstration repository:

[Example Apps to Demonstrate Argo CD](https://github.com/argoproj/argocd-example-apps)

To create an example app or real-world deployment, just open a browser, log in to Argo UI and after logging in, click the + New App button as shown below:

![](https://miro.medium.com/max/305/1*_6d_P4pR3xwIbcHteHPFtQ.png)

Define your app’s name, use the project option “default”, and leave the sync policy as “manual”:

![](https://miro.medium.com/max/441/1*vNIW5dEawgETRrllUhUsFA.png)

Connect the [https://github.com/argoproj/argocd-example-apps.git](https://github.com/argoproj/argocd-example-apps.git) repo to Argo CD by setting the repository URL to the GitHub repo URL.

![](https://miro.medium.com/max/418/1*TfmZ3S9iZe-J6mpnS-2T6w.png)

For Destination, set cluster to in-cluster and namespace to default:

![](https://miro.medium.com/max/263/1*aZWMMUqkTCncYq2ojCC8wQ.png)

Then, create and deploy via:


```bash
argocd app get <YOUR-APPLICATION-NAME>
argocd app sync <YOUR-APPLICATION-NAME>
```

or just sync via UI:

![](https://miro.medium.com/max/700/1*veKlucEegflqruxdCtPXFg.png)

Starting from the example deployments above, you can now run your GitOps operations on Argo CD.

Example app’s deployment manifest:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: guestbook-ui
spec:
  replicas: 1
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      app: guestbook-ui
  template:
    metadata:
      labels:
        app: guestbook-ui
    spec:
      containers:
      - image: gcr.io/heptio-images/ks-guestbook-demo:0.2
        name: guestbook-ui
        ports:
        - containerPort: 80
```
