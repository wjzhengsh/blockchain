---

copyright:
  years: 2019
lastupdated: "2019-05-16"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Kubernetes
{: #k8s-overview}

The {{site.data.keyword.blockchainfull_notm}} Platform allows you to provision blockchain components into your {{site.data.keyword.cloud_notm}} Kubernetes cluster. Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.
{:shortdesc}

Kubernetes provides a container-centric management environment. It orchestrates computing, networking, and storage infrastructure on behalf of user workloads. This provides much of the simplicity of Platform as a Service (PaaS) with the flexibility of Infrastructure as a Service (IaaS), and enables portability across infrastructure providers.

The following diagram explains the architecture of Kubernetes. For more explanation about nodes, containers, and pod, see the [Key Kubernetes objects](#k8s-overview-key-obj) section below.

![Kubernetes architecture diagram](../images/k8s-archi-diagram.svg "{{site.data.keyword.cloud_notm}} Kubernetes Service architecture")
*Figure 1. Kubernetes architecture diagram*


## {{site.data.keyword.cloud_notm}} Kubernetes Service
{: #k8s-overview-iks}

{{site.data.keyword.cloud_notm}} Kubernetes Service delivers powerful tools by combining Docker containers, the Kubernetes technology, an intuitive user experience, and built-in security and isolation to automate the deployment, operation, scaling, and monitoring of containerized apps in a cluster of compute hosts.

For more information about {{site.data.keyword.cloud_notm}} Kubernetes Service, see the following topics in {{site.data.keyword.cloud_notm}} Kubernetes Service documentation:
- [{{site.data.keyword.cloud_notm}} Kubernetes Service technology ![External link icon](../images/external_link.svg "External link icon")](/docs/containers/cs_tech.html#ibm-cloud-kubernetes-service-technology "{{site.data.keyword.cloud_notm}} Kubernetes Service technology documentation")
- [Why {{site.data.keyword.cloud_notm}} Kubernetes Service ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-cs_ov#cs_ov "Why {{site.data.keyword.cloud_notm}} Kubernetes Service documentation")
- [Defining your Kubernetes strategy ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-strategy#strategy "Defining your Kubernetes strategy")


## Key Kubernetes objects
{: #k8s-overview-key-obj}

- **Cluster**

  A set of machines, called nodes, that run containerized applications managed by Kubernetes. A cluster has several worker nodes and at least one master node.

- **Node**

  A node is a worker machine in Kubernetes. A node may be a VM or physical machine, depending on the cluster. Each node contains the services necessary to run pods and is managed by the master components. The services on a node include the container runtime, `kubelet` and `kube-proxy`. For more information, see the [Kubernetes Node section ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/architecture/nodes/ "Kubernetes Node section") in the Kubernetes documentation.

- **Container**

  A lightweight and portable executable image that contains software and all of its dependencies. Containers decouple applications from underlying host infrastructure to make deployment easier in different cloud or OS environments, and for easier scaling.

- **Pod**

  The smallest and simplest Kubernetes object. A Pod represents a set of running containers on your cluster. A Pod is typically set up to run a single primary container. It can also run optional sidecar containers that add supplementary features like logging. Pods are commonly managed by a Deployment. For more information, see the [Kubernetes Pod section ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/workloads/pods/pod/) in the Kubernetes documentation.
