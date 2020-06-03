---
title: Kubernetes/Helm For Dummies
date: 2020-06-02 15:44:18.000000000 -07:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- General
- Overview
- Automation
- Container Orchestration
- Microservices
- Containers
- Kubernetes
- Helm
tags:
- Kubernetes
- Containers
- Docker
- Helm
meta:
  _wpcom_is_markdown: '1'
  _publicize_job_id: '30813365882'
  timeline_notification: '1557873861'
permalink: "/2020/06/02/k8s-helm-for-dummies/"
---
# Introduction
I just wanted to start off by saying that I am no expert in Kubernetes (i.e. k8s) or Helm, but I have learned a lot over the past year, and want to share my knowledge. Kubernetes is such a complex subject, but IMO, I think it is a great tool to automate DevOps.

## Kubernetes
According to the [official k8 docs](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/), Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. Kubernetes is a tool that can help run distributed systems resiliently (or in a fault-tolerant way). From taking care of scaling (vertical and horizontal) and failover for your application to making it easy to rollout deployments.

Pretty much from a high-level, Kubernetes is in charge of running workloads by placing containers in Pods to run on Nodes. There can be multiple containers in a Pod. Pods can be thought of as the smallest unit of deployment for Kubernetes (very low level that you probably don't have to use directly)

### Kubernetes API Resources
I'll briefly talk about some of the resources I commonly use in Kubernetes. Usually, I opt towards a declarative way to define the Kubernetes resources using yaml files to ensure reproducibility.

#### Controller API Objects
- **Deployment**
  - A controller and a higher level abstraction that controls Replicasets and Pods. It provides declarative updates for Pods and ReplicaSets. You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate.
  - Make sure to set the request and limit to the same value to ensure Guaranteed Quality of Service. [Reference](https://blog.pipetail.io/posts/2020-05-04-most-common-mistakes-k8s/)
  - I usually use a lot of these for defining multiple services & their respective application code
- **Job**:
  - Another controller that is a higher level abstraction of Pods that can create one or more Pods to run a one-time job on a Pod until completion.
  - I usually run these for database migrations or backfills of databases.
- **CronJob**
  - Another controller similar to the Job, but instead, this creates Jobs on a repeating schedule.

#### Networking API Objects
- **Service**
  - Route traffic internally to your code from within the cluster. Abstraction which defines a logical set of Pods and a policy by which to access them. Service routes traffic across a set of Pods: Pods can communicate across Nodes.
  - Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic. Services can be exposed in different ways by specifying a type in the ServiceSpec.
    - Service Types:
      - ClusterIP: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType.
      - NodePort: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting \<NodeIP\>:\<NodePort\>.
      - LoadBalancer: Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
- **Ingress**
  - Routes external traffic into the cluster to your code
  - An API object that manages external access to the services in a cluster, typically HTTP.
  - Ingress can provide load balancing, SSL termination and name-based virtual hosting.
  - Typically, a best practice is to have a single nginx ingress controller exposed to single Load Balancer in your cluster.
    - The nginx ingress controller will route the traffic in the cluster based on Kubernetes ingress resources (defined for each project or HTTP service)
      - Usually 2 ingress resources: public & internal
    - LB -> NGINX INGRESS CONTROLLER -> INGRESS RESOURCE -> SERVICE (APPLICATION)
    - Reference: See [LoadBalancer for every HTTP service](https://blog.pipetail.io/posts/2020-05-04-most-common-mistakes-k8s/)

#### Autoscaling API Objects
- **Horizontal Pod Autoscaler**
  - Automatically scales the number of pods in a replication controller, deployment, replica set or stateful set based on observed CPU utilization (or, with custom metrics support, on some other application-provided metrics)
    - Custom metric: can scale based on number of unacked PubSub messages

#### Policy API Object
- **Pod Disruption Budget**
  - Limits the number of pods of a replicated application that are down simultaneously from voluntary disruptions to ensure high availability. Built on top of Deployments.
  - Essentially lower bound of allowed Pods at a time.
    - This pretty much guarantees that there's at least 1 Pod (or minPods) up at all times (during deployment, during failovers, and other automated cluster actions)
    - Good for webservers or APIs.

## Helm
Helm is a package manager for Kubernetes. The common example is what `brew` is to MacOS and what `apt-get` is to Linux distributions is what `helm` is to Kubernetes.

Reference: https://helm.sh/docs/intro/using_helm/

Three core concepts in Helm are: Chart, Repository, and Release.
- A Chart is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster.
- A Repository is the place where charts can be collected and shared. It's like Perl's CPAN archive or the Fedora Package Database, but for Kubernetes packages.
- A Release is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new release is created.

Summarized as: Helm installs charts into Kubernetes, creating a new release for each installation. And to find new charts, you can search Helm chart repositories.

### Helm Templating
Helm uses and extends Go templates for templating your resource files. Pretty much what this means is you declaratively define required variables (e.g. environment variables) in a yaml file and key/value form in one place, and inject them into the Kubernetes resource template files. This is nice, because you can define environment variables in one place, and inject them into multiple Kubernetes resource files.

Usually, you have multiple values file to inject into your templates depending on the environment (prod `values-prod.yaml` vs. dev `values.yaml`). Note, Helm will either extend the values in dev (in the `values.yaml` file) if not specified in the `values-prod.yaml`, or replace the values in `values.yaml` with `values-prod.yaml` if it is defined.

### Some Helm Files
- Chart.yaml: Defines the Chart
- NOTES.txt: The “help text” for your chart. This will be displayed to your users when they run helm install.
- _helpers.tpl: A place to put template helpers that you can re-use throughout the chart
- .helmignore: Ignore files for Helm Chart
- requirements.yaml: Specify Helm Chart dependencies (e.g. Redis)
- values-prod.yaml: Define environment variables for Prod
- values.yaml: Defines environment variables for Prod and Dev


### Helm Practical Advice
I spent a week trying to understand Helm and the templating, and realized I would just forget it, since I didn't use it often. My advice is to use it on a project and learn from there. Typically, I copy-and-paste Helm templates from online source, such as https://github.com/helm/charts/tree/master/stable. Or reference other projects that use Helm. I think that was the best way for me to kind of learn. IMO, Kubernetes is a prequisite of learning Helm, since Helm is just an essentially a layer above Kubernetes to make things easier.

