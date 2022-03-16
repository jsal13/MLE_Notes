# Kubeflow
#kubeflow #k8s #pipelines

> The Kubeflow project is dedicated to making deployments of machine learning (ML) workflows on Kubernetes simple, portable and scalable. Our goal is not to recreate other services, but to provide a straightforward way to deploy best-of-breed open-source systems for ML to diverse infrastructures. Anywhere you are running Kubernetes, you should be able to run Kubeflow.

This seems promising, as K8s is fairly ubiquitious in most pipelines anyhow.

## Setting Up Pipelines
In order to get this running, I needed to install a few things.  I was working on a Windows machine, but the same steps will work for \*nix machines.

(Derived from [this](https://www.kubeflow.org/docs/components/pipelines/installation/localcluster-deployment/) tutorial.)

1. Install [kubectl](https://kubernetes.io/docs/tasks/tools/).
2. Install [k3d](https://community.chocolatey.org/packages/k3d/).
3. Create a cluster: `k3d cluster create testcluster`.
4. Run the following commands to set up the kubeflow pipelines.
```shell
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=1.8.1"
kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-pns?ref=1.8.1"
```
5. Wait a few minutes.  It takes a bit for the pipelines to deploy.
6. 



5. 



## Pipelines

A [[Pipeline]] consists of:
- A UI for managing and tracking experiments, jobs, and runs.
- An engine for scheduling multi-step ML workflows.
- An SDK for defining and manipulating pipelines and components.
- Notebooks for ineracting with the system using the SDK.

The Pipelines project was created to solve the following issues:
- End-to-End orchestration of ML Pipelines.
- Easy experimentation.
- Easy re-use of components.

---
A [[pipeline]] is a description of an ML workflow, including all of the components and how they combine to form a graph.  

A [[pipeline component]] is a self-contained set of user-code, packaged as a Docker Image, which performs a _single step_ in the pipeline.  For example, a pipeline component could be _preprocessing_  or _transformation_ or _training_, etc.  [[Examples|This example]] details things you can do in the pipeline.

