# **[PART 1]{.ul}**

VMware CNA team in Singapore collaborates with industry software vendors
to take advantage of cloud computing, microservices and new technologies
to advance their competitiveness. In a recent engagement, we conducted a
PoC around the subject of containerizing Machine Learning workload to
run on Kubernetes. The positive outcome demonstrates exciting potential
for the software vendor to accelerate development and deployment speed
while reducing cost and enhance availability.

# Introduction

The data science and machine learning domains are rapidly evolving.
Unprecedented speed of advancement drove new applications and use cases
that propels computing to new frontiers. This could only take place with
faster and more power computing components, namely the CPU and more
importantly, the GPU.

The GPU compose of hundreds of cores which allows it to break complex
problems into thousands or millions of separate tasks and work them out
at once. It provides faster turnaround time for one of the most
important research domains today: Machine Learning. However, coupled
with the screaming performance are the downsides, such as cost, power
consumption, heat considerations, etc.

Given sufficient budget, it is not uncommon to find a typical data
scientist team to host multiple machines, each with one or more GPU
cards that sit natively on the operating system. The training code is
then loaded onto the platform to carry out its task. Another popular
deployment model is to replace the physical servers with Virtual
Machines, sharing parts of the GPU running off the physical host.

However, both models are inefficient in several ways.

1.  Idling GPU -- when an application runs code that utilizes the GPU,
    no other applications on the same host/virtual machine can use the
    GPU. It has to queue and wait for the current process to complete.

2.  Hard partitioning -- Workload that are dynamic in nature may need
    different shares of the GPU at different times. By partitioning the
    GPU and allocate to Virtual Machine, applications that are starve of
    GPU resources (think of your newest, shiny models) cannot leverage
    on the spare capacity available on rest of the Virtual Machines.

3.  Repeatability -- Running machine learning models through executing
    source code is slowly getting phased out by containers, which
    addresses the headaches of environment setup, libraries
    compatibility and code consistency.

As Kubernetes becomes the mainstream platform, we see more and more
workload of different characteristics being deployed. It evolved from
the benign simple microservices, to stateful application and now,
Machine Learning.

Running Machine Learning workloads to run on Kubernetes involves several
steps. First, the workload has to be containerized and secured. Then,
the communication layer between Kubernetes and GPU needs to be enabled.
Next, setup the persistence layer needed for workload to run. Lastly,
put in place management and monitoring platform to track for errors and
running status.

We achieve this using a combination of tools and technologies. Through
integrating Tanzu Kubernetes Grid (TKG) with Bitfusion, running on
vSphere 7 with Dell EMC PowerEdge R740 and NVIDIA V100 GPU cards, we
were able to demonstrate containerized Machine Learning workload running
almost natively, using the NVIDIA V100 GPU cards.

It is interesting to note the important role played by VMware vSphere
Bitfusion. Bitfusion

was used to provide AI/ML application access to GPU power across the
entire data center, on demand. It allows applications who don't have
GPUs on their VMs to make use of a pool of GPUs remotely over the
network. Each AI/ML application can request multiple or partial GPUs
dynamically without the need to allocate fixed profile to a VM.

It is equivalent to empowering all the workload with GPU without
spending additional dime.

In the subsequent series, we will share further on the solution
architecture and how it works.

***Next**:*

-   Solution Overview & Architecture

-   Solution Setup

# **[PART 2]{.ul}**

In the previous blog post, we highlighted some of the challenges and
downside of deploying machine learning workload on bare metal machines
attached with GPU cards. The solution was to run the workload in
Kubernetes using VMware Bitfusion which virtualizes the GPU.

In this article, we are going to dive deeper to explain how the setup
described above works.

# Solution Overview & Architecture

A high level overview of the solution architecture is shown below

![Graphical user interface Description automatically
generated](media/image1.jpg){width="6.263888888888889in"
height="2.8201388888888888in"}

Figure 1 Platform Architecture

It should be distinguished from above that two clusters exist in this
setup: Compute Cluster and GPU Server Farm.

The Compute Cluster consists of a minimum of THREE (3) physical Dell
server hosts with vCenter Server, vSphere with Tanzu, NSX-T Data Center
and TKG workload cluster for container workloads deployed on these
server hosts. In our PoC, the AI/ML applications were deployed in the
Compute Cluster in the form of container pod.

The GPU Server Farm consists of one of multiple physical Dell server
hosts with vSphere Bitfusion server VMs and hardware accelerators such
as NVIDIA graphical processing units (GPUs) cards installed on each
server host.

VMware vSphere Bitfusion decouples physical resources from servers
within an environment. Bitfusion virtualizes GPUs to provide a pool of
shared, network-accessible resources that support artificial
intelligence (AI) and machine learning (ML) workloads. The platform can
share GPUs in a virtualized infrastructure, as a pool of
network-accessible resources, rather than isolated resources per server.

vSphere Bitfusion has a client-server architecture. The Bitfusion server
is the virtual appliance, and its OVA is deployed in the GPU Server
Farm. On the other hand, on the client side, provided libraries are used
to open files, allocate memory, and call CUDA as if operating on a
machine with local GPUs.

How It works

The following diagram walks through the end-to-end execution flow
between the agent in the Bitfusion client to the GPUs in the Bitfusion
server

![Diagram Description automatically
generated](media/image2.jpg){width="6.263888888888889in"
height="2.8201388888888888in"}

Figure Execution Flow

1.  Machine Learning workload initiated within the Container/Pod is
    captured and redirected to Bitfusion runtime.

2.  The Bitfusion runtime translates the request and sends the requests
    over the network to Bitfusion Server

3.  Bitfusion server receives incoming request and translates back the
    requests.

4.  Bitfusion server executes the toolkits, libraries on the GPU
    assigned to it.

5.  The execution output is translated and send back to the originator

6.  Bitfusion runtime at Container/Pod translates back the result and
    returns result back to application.

7.  Application continues.

# Technology Overview

## VMWare vSphere 7 Enterprise Plus

![](media/image3.png){width="0.7201388888888889in"
height="0.7201388888888889in"}VMware vSphere is a server virtualization
product that combines the VMware ESXi hypervisor and VMware vCenter
server, which transforms data centers into aggregated computing
infrastructures that include CPU, storage and networking resources.

## VMware vSphere Bitfusion 3.0

VMware vSphere Bitfusion virtualizes hardware accelerators such as
graphical processing units (GPUs) to provide a pool of shared,
network-accessible resources that support artificial intelligence (AI)
and machine learning (ML) workloads. VMware vSphere Bitfusion is based
on a client/server model architecture which allows you to attach GPUs to
any machine remotely, offering reduction in total cost of ownership.
Bitfusion provides the capabilities to slice a single GPU into multiple
virtual GPUs dynamically of any size, without rebooting the virtual
machine, which increases the utilization due to the enablement of
running multiple workloads in parallel on the same GPU.

![Graphical user interface, diagram Description automatically
generated](media/image4.jpg){width="6.263888888888889in"
height="2.60625in"}

Figure 3 Bitfusion Client/Server Model Architecture

## VMware vSphere with Tanzu

![](media/image5.png){width="2.7291666666666665in"
height="0.7555555555555555in"}vSphere with Tanzu, part of VMware Tanzu
portfolio, is the new generation of vSphere for containerized
applications. It transforms vSphere to a platform for running Kubernetes
workloads natively on the hypervisor layer. When enabled on a vSphere
cluster, vSphere with Tanzu (with Supervisor Cluster) provides the
capability to run Kubernetes workloads directly on ESXi hosts and to
create upstream Kubernetes clusters (TKG workload cluster) within
dedicated resource pools. VMware Tanzu Kubernetes Grid (TKG) simplifies
the deployment and management of Kubernetes clusters with Day 1 and Day
2 operations support. TKG manages container deployment from the
application layer all the way to the infrastructure layer. TKG supports
high availability, autoscaling, health- checks, and self-repairing of
underlying VMs and rolling upgrades for the Kubernetes clusters.

## VMware NSX-T

![](media/image6.png){width="1.7277777777777779in"
height="0.7736111111111111in"}VMware NSX is best known as the
software-defined networking (SDN) and security platform. VMware NSX-T
Data Center provides an agile software-defined infrastructure to build
cloud-native application environments. VMware NSX-T Data Center provides
network connectivity to the objects inside the Supervisor Cluster and
external networks. The Supervisor Cluster from vSphere with Tanzu uses
the NSX-T Data Center to provide connectivity to Kubernetes control
plane VMs, services, and workloads

## Dell EMC PowerEdge R740

The PowerEdge R740xd server provides the benefit of scalable storage
performance and data set processing. It maximizes application
performance with the optimal mix of accelerator cards, storage and
compute power in a 2U, 2-socket platform optimized for VDI. In our POC,
the Compute Cluster and GPU Server Farm are both hosted by a set of
PowerEdge R740 servers

![Amazon.com: Dell EMC PowerEdge R740 Server Bundle with Bronze 3104
1.7GHz 6C 16GB RAM H730P 2x300GB (Certified Refurbished): Computers
&amp; Accessories](media/image7.jpeg){width="5.138888888888889in"
height="1.0101159230096237in"}

## NVIDIA GPU Tesla V100 32GB

![](media/image8.jpeg){width="1.3451388888888889in"
height="1.0763888888888888in"}The NVIDIA V100 GPU powered by NVIDIA
Volta architecture is the most widely used accelerator for scientific
computing and artificial intelligence, it is the most advanced data
centre GPU ever built to accelerate AI, data science. In our POC, we
have 2 NVIDIA V100 GPU with 32GB configuration on the physical server in
the GPU Server Farm

In the last article, we will share with you on how we setup the
Bitfusion server and client, and then deploy the application in the
Compute Cluster.

***Previous**:*

-   Introduction

***Next**:*

-   Solution Setup

# **[PART 3]{.ul}**

# Solution Setup

In the last article, we will walkthrough with you on we setup the
Bitfusion server and client, and then deploy the application in the
Compute Cluster. We wish to highlight that our blog article series is to
showcase the integration of Tanzu Kubernetes Grid (TKG) with Bitfusion,
running on vSphere 7 with Dell EMC PowerEdge R740 and NVIDIA V100 GPU
cards **in high level** and we are not going to go through the setup and
configuration in details. So, for Bitfusion configuration, you may refer
to the [VMware vSphere Bitfusion Installation
Guide](https://docs.vmware.com/en/VMware-vSphere-Bitfusion/3.0/vsphere-bitfusion-install-guide-30.pdf).

## Bitfusion Server Setup and Configuration

All GPU resources are consolidated in the GPU Server Farm. The Bitfusion
servers are sourced from this cluster where they are direct attached to
the GPU in pass-through mode. You will need to deploy the Bitfusion
Server ([OVA
file](https://my.vmware.com/web/vmware/downloads/details?downloadGroup=BITFUSION-300&productId=974))
to the ESXi host with GPU cards installed. Before we power up the
virtual appliance, we will need to edit the hardware settings to
allocate the memory, disk space and most importantly the GPU cards to
the Bitfusion server appliance. In our PoC, we have 2 NVIDIA GPU Cards
(32GB each) installed on a single ESXi host, and we dedicate the 2 GPU
cards to the a single Bitfusion Server (PCI device 0 and PCI device 1 in
Figure 3 Bitfusion Servers with Pass-Through GPU)

![Graphical user interface, application Description automatically
generated](media/image9.png){width="4.982417979002625in"
height="4.4925404636920385in"}

Figure 3 Bitfusion Servers with Pass-Through GPU

## Configuration of AI/ML application as Bitfusion Client

The next step is to enable our containerized AI/ML application as a
Bitfusion Client. In order to do that, we will need to request for a new
Client Authentication Token from Bitfusion console in vCenter.

![Graphical user interface, application, Teams Description automatically
generated](media/image10.png){width="6.263888888888889in"
height="1.1479166666666667in"}

Figure 4 Request for Bitfusion Client Authenication Token

Select the newly created token and download the tar file to your local
machine. Unzip the tar file and copy the extracted files (ca.crt,
client.yaml and servers.conf) into a temp folder. In our case, we create
a temp folder called "bitfusion-files". Other than the token, we will
also need to download the Bitfusion Client RPM package into the same
folder. The base OS image for our AI/ML application is Ubuntu, so for
our case we have the
[bitfusion-client-ubuntu2004_3.0.0-11_amd64.deb](https://my.vmware.com/web/vmware/downloads/details?downloadGroup=BITFUSION-300&productId=974)
download in to the folder.

The last step in this section is to rebuild the AI/ML application
container image to include the Bitfusion client token and the RPM file.
For this, we add the following to the Docker file:

+-----------------------------------------------------------------------+
| #\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-         |
| \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-- |
|                                                                       |
| \# Copy bitfusion client related files                                |
|                                                                       |
| #\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-         |
| \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-- |
|                                                                       |
| RUN mkdir -p /root/.bitfusion                                         |
|                                                                       |
| COPY ./bitfusion-files/client.yaml /root/.bitfusion/client.yaml       |
|                                                                       |
| COPY ./bitfusion-files/servers.conf /etc/bitfusion/servers.conf       |
|                                                                       |
| RUN mkdir -p /etc/bitfusion/tls                                       |
|                                                                       |
| COPY ./bitfusion-files/ca.crt /etc/bitfusion/tls/ca.crt               |
|                                                                       |
| #\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-         |
| \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-- |
|                                                                       |
| \# Update package list                                                |
|                                                                       |
| \# Install Bitfusion. Use deb file for Ubuntu20.04                    |
|                                                                       |
| #\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-         |
| \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-- |
|                                                                       |
| \# Set initial working directory                                      |
|                                                                       |
| RUN mkdir -p /workspace/bitfusion/batch-scripts                       |
|                                                                       |
| WORKDIR /workspace/bitfusion                                          |
|                                                                       |
| \# Copy Release version of bitfusion client                           |
|                                                                       |
| COPY ./bitfusion-files/bitfusion-client-ubuntu2004_3.0.0-11_amd64.deb |
| .                                                                     |
|                                                                       |
| RUN apt-get update \\                                                 |
|                                                                       |
| && apt-get install -y                                                 |
| ./bitfusion-client-ubuntu2004_3.0.0-11_amd64.deb \\                   |
|                                                                       |
| && apt-get install -y open-vm-tools \\                                |
|                                                                       |
| && \\                                                                 |
|                                                                       |
| rm -rf /var/lib/apt/lists/                                            |
+=======================================================================+
+-----------------------------------------------------------------------+

## Kubernetes Setup

Once the container image is rebuilt, we are going to deploy the
application to the Kubernetes platform. Since we are to deploy the
application on-premise, we will create a new supervisor namespace
("cnasg") and then deploy a new TKG workload cluster ("cluster-01") in
that namespace. The administrator/IT operator can allocate maximum CPU,
memory and storage size for this particular namespace, and then assign
the developers (the users) who can access to this namespace. Developers
who are not assigned to this namespace are not allowed to access and
manage the pods and TKG workload clusters in this namespace.

![A screenshot of a computer Description automatically
generated](media/image11.png){width="6.263888888888889in"
height="0.9194444444444444in"}

Figure 5 Workload Management to create New Namespace

![A screenshot of a computer Description automatically generated with
medium confidence](media/image12.png){width="6.263888888888889in"
height="3.834722222222222in"}

Figure 6 Resource Allocation and User Access of the newly created
Namespace

Refer to the
[details](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-1544C9FE-0B23-434E-B823-C59EFC2F7309.html)
of the vSphere supervisor namespace configuration and management.

Once the supervisor namespace is created, we will then need to create a
cluster specification defined using YAML. For our PoC, we define a
cluster with 1 master node (controlplane) and 3 worker nodes, with 4 CPU
and 32GB of RAM for each node
("[best-effort-xlarge](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-7351EEFF-4EF0-468F-A19B-6CEA40983D3D.html)")

+-----------------------------------------------------------------------+
| apiVersion: run.tanzu.vmware.com/v1alpha1                             |
|                                                                       |
| kind: TanzuKubernetesCluster                                          |
|                                                                       |
| metadata:                                                             |
|                                                                       |
| name: cluster-01                                                      |
|                                                                       |
| spec:                                                                 |
|                                                                       |
| distribution:                                                         |
|                                                                       |
| version: v1.20.2                                                      |
|                                                                       |
| topology:                                                             |
|                                                                       |
| controlPlane:                                                         |
|                                                                       |
| count: 1                                                              |
|                                                                       |
| class: best-effort-xlarge                                             |
|                                                                       |
| storageClass: bf-k8s                                                  |
|                                                                       |
| workers:                                                              |
|                                                                       |
| count: 3                                                              |
|                                                                       |
| class: best-effort-xlarge                                             |
|                                                                       |
| storageClass: bf-k8s                                                  |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 7 TKG Workload Cluster specification in YAML

In order to deploy the YAML file to create a new cluster, we will need
to download and install the Kubernetes CLI Tools for vSphere. Using the
vSphere Plugin for kubectl, authenticate with the Supervisor Cluster,
and then run the YAML file to create the cluster ("cluster-01")

![Graphical user interface, text Description automatically
generated](media/image13.png){width="2.395138888888889in"
height="1.023121172353456in"}

![Graphical user interface, text Description automatically
generated](media/image13.png){width="2.3954735345581804in"
height="0.9891021434820647in"}

Figure 8 TKG Workload Cluster ("cluster-01") under the Namespace
("cnasg")

+-----------------------------------------------------------------------+
| \# kubectl vsphere login \--insecure-skip-tls-verify                  |
| \--server=172.24.206.1 \--vsphere-username administrator\@aiml.local  |
| \--tanzu-kubernetes-cluster-namespace=cnasg                           |
|                                                                       |
| Password:                                                             |
|                                                                       |
| Logged in successfully.                                               |
|                                                                       |
| You have access to the following contexts:                            |
|                                                                       |
| 172.24.206.1                                                          |
|                                                                       |
| cnasg                                                                 |
|                                                                       |
| If the context you wish to use is not in this list, you may need to   |
| try                                                                   |
|                                                                       |
| logging in again later, or contact your cluster administrator.        |
|                                                                       |
| To change context, use \`kubectl config use-context \<workload        |
| name>\`                                                               |
|                                                                       |
| \# kubectl config use-context cnasg                                   |
|                                                                       |
| Switched to context \"cnasg\".                                        |
|                                                                       |
| \# kubectl get nodes                                                  |
|                                                                       |
| NAME STATUS ROLES AGE VERSION                                         |
|                                                                       |
| 421c4213dca964f747952ed1167baf5f Ready master 71d v1.19.1+wcp.3       |
|                                                                       |
| 421cb28ba406a7b537e9859febd31b78 Ready master 71d v1.19.1+wcp.3       |
|                                                                       |
| 421cd947d93311a39788ce55908fed3a Ready master 71d v1.19.1+wcp.3       |
|                                                                       |
| esx209-162 Ready agent 71d v1.19.1-sph-b0161d9                        |
|                                                                       |
| esx209-163 Ready agent 71d v1.19.1-sph-b0161d9                        |
|                                                                       |
| esx209-164 Ready agent 71d v1.19.1-sph-b0161d9                        |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 9 Kubernetes login to Supervisor Namespace ("cnasg") and Node
Listing for the Supervisor Cluster

+-----------------------------------------------------------------------+
| \# kubectl apply -f tkc-cluster-01.yaml                               |
|                                                                       |
| tanzukubernetescluster.run.tanzu.vmware.com/cluster-01 created        |
|                                                                       |
| \# kubectl get tkc                                                    |
|                                                                       |
| NAME CONTROL PLANE WORKER DISTRIBUTION AGE PHASE TKR COMPATIBLE       |
| UPDATES AVAILABLE                                                     |
|                                                                       |
| cluster-01 1 1 v1.20.2+vmware.1-tkg.2.3e10706 7m26s running True      |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 10 TKG Workload Cluster ("cluster-01") provisioning with kubectl
CLI and YAML file

+-----------------------------------------------------------------------+
| \# kubectl-vsphere login \--insecure-skip-tls-verify                  |
| \--server=172.24.206.1 \--vsphere-username administrator\@aiml.local  |
| \--tanzu-kubernetes-cluster-namespace=cnasg                           |
| \--tanzu-kubernetes-cluster-name=cluster-01                           |
|                                                                       |
| Password:                                                             |
|                                                                       |
| Logged in successfully.                                               |
|                                                                       |
| You have access to the following contexts:                            |
|                                                                       |
| 172.24.206.1                                                          |
|                                                                       |
| cluster-01                                                            |
|                                                                       |
| cnasg                                                                 |
|                                                                       |
| If the context you wish to use is not in this list, you may need to   |
| try                                                                   |
|                                                                       |
| logging in again later, or contact your cluster administrator.        |
|                                                                       |
| To change context, use \`kubectl config use-context \<workload        |
| name>\`                                                               |
|                                                                       |
| \# kubectl config use-context cluster-01                              |
|                                                                       |
| Switched to context \"cluster-01\".                                   |
|                                                                       |
| \# kubectl get nodes                                                  |
|                                                                       |
| NAME STATUS ROLES AGE VERSION                                         |
|                                                                       |
| cluster-01-control-plane-89qkw Ready control-plane,master 8d          |
| v1.20.2+vmware.1                                                      |
|                                                                       |
| cluster-01-workers-5xbnr-8f69f9d46-cgz87 Ready \<none> 8d             |
| v1.20.2+vmware.1                                                      |
|                                                                       |
| cluster-01-workers-5xbnr-8f69f9d46-hcnkm Ready \<none> 8d             |
| v1.20.2+vmware.1                                                      |
|                                                                       |
| cluster-01-workers-5xbnr-8f69f9d46-ksjfw Ready \<none> 8d             |
| v1.20.2+vmware.1                                                      |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 11 Kubernetes login and Node Listing for the TKG Workload Cluster
("cluster-01")

Refer to the
[details](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-2597788E-2FA4-420E-B9BA-9423F8F7FD9F.html)
to provision the TKG workload cluster

Finally, we are ready to deploy our AI/ML application on our newly
created TKG workload cluster. In our PoC, we created a YAML file to
define the Pod specification of the AI/ML application. The container
image of the AI/ML application ("pytorch-mock-bitfusion") is stored in
the private registry with the embedded Harbor Image Registry in vSphere.
Also, do take note that in the YAML file, we requested for 80%
(PARTIAL_GPU=0.8) of the GPU resources from the Bitfusion Server with 2
GPU cards (NUM_GPUS=2) in the environment variables.

+-----------------------------------------------------------------------+
| apiVersion: v1                                                        |
|                                                                       |
| kind: Pod                                                             |
|                                                                       |
| metadata:                                                             |
|                                                                       |
| labels:                                                               |
|                                                                       |
| run: pod-pytorch-mock-bitfusion                                       |
|                                                                       |
| name: pod-pytorch-mock-bitfusion                                      |
|                                                                       |
| spec:                                                                 |
|                                                                       |
| containers:                                                           |
|                                                                       |
| \- name: pod-pytorch-mock-bitfusion                                   |
|                                                                       |
| image: 172.24.206.3/cnasg/pytorch-mock-bitfusion:latest               |
|                                                                       |
| imagePullPolicy: \"Always\"                                           |
|                                                                       |
| command: \[\"/workspace/dlbs/run_pytorch.sh\"\]                       |
|                                                                       |
| env:                                                                  |
|                                                                       |
| \- name: BATCH_SIZE                                                   |
|                                                                       |
| value: \"64\"                                                         |
|                                                                       |
| \- name: OUTPUT_PATH                                                  |
|                                                                       |
| value: \"/tmp/data/\"                                                 |
|                                                                       |
| \- name: NUM_GPUS                                                     |
|                                                                       |
| value: \"2\"                                                          |
|                                                                       |
| \- name: PARTIAL_GPU                                                  |
|                                                                       |
| \# Change value to the required fraction of GPU                       |
|                                                                       |
| value: \"0.8\"                                                        |
|                                                                       |
| \- name: USER                                                         |
|                                                                       |
| value: \"cnasg-user\"                                                 |
|                                                                       |
| volumeMounts:                                                         |
|                                                                       |
| \- name: data-vol                                                     |
|                                                                       |
| mountPath: /tmp/data/                                                 |
|                                                                       |
| imagePullSecrets:                                                     |
|                                                                       |
| \- name: cred-pytorch-mock-bitfusion                                  |
|                                                                       |
| restartPolicy: Never                                                  |
|                                                                       |
| dnsPolicy: ClusterFirst                                               |
|                                                                       |
| volumes:                                                              |
|                                                                       |
| \- name: data-vol                                                     |
|                                                                       |
| persistentVolumeClaim:                                                |
|                                                                       |
| claimName: pvc-pytorch-mock-bitfusion                                 |
|                                                                       |
| \-\--                                                                 |
|                                                                       |
| apiVersion: v1                                                        |
|                                                                       |
| kind: PersistentVolumeClaim                                           |
|                                                                       |
| metadata:                                                             |
|                                                                       |
| name: pvc-pytorch-mock-bitfusion                                      |
|                                                                       |
| spec:                                                                 |
|                                                                       |
| accessModes:                                                          |
|                                                                       |
| \- ReadWriteOnce                                                      |
|                                                                       |
| resources:                                                            |
|                                                                       |
| requests:                                                             |
|                                                                       |
| storage: 10Gi                                                         |
|                                                                       |
| storageClassName: bf-k8s                                              |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 12 Pod specification for AI/ML application in YAML

We run the YAML file with kubectl CLI and the application logs are
generated as below. You will notice that from the log file below there
are 2 GPUs locked with partial memory of 0.8 (80%) reserved for this
application. Once the task is completed, the GPU resources will then be
released back to the GPU virtual pool.

+-----------------------------------------------------------------------+
| \[INFO\] 2021-07-02T07:07:28Z Query server 172.24.210.80:56001 to     |
| claim client id: 60475feb-34c2-46e1-9b90-5ded85ed96bf                 |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:28Z Query server 172.24.210.80:56001 gpu    |
| availability                                                          |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:28Z Query server health status              |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:30Z Choosing GPUs from server list          |
| \[172.24.210.80:56001\]                                               |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:30Z Requesting GPUs \[0 1\] with 26008 MiB  |
| of memory from server 0, with version 3.0.0-f95ba24a\...              |
|                                                                       |
| Requested resources:                                                  |
|                                                                       |
| Server List: 172.24.210.80:56001                                      |
|                                                                       |
| Client idle timeout: 0 min                                            |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:30Z Locked 2 GPUs with partial memory 0.8,  |
| configuration saved to \'/tmp/bitfusion126498944\'                    |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:30Z Running client command \'python         |
| model.py \--batch-size 64 \--output-path /tmp/data/ \--save-model\'   |
| on 2 GPUs, with the following servers:                                |
|                                                                       |
| \[INFO\] 2021-07-02T07:07:30Z 172.24.210.80 55001                     |
| 6e9df987-a6e8-4d18-9476-9ea12a419e83 56001 3.0.0-f95ba24a             |
|                                                                       |
| Downloading                                                           |
| http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz           |
|                                                                       |
| Downloading                                                           |
| http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz           |
|                                                                       |
| Downloading                                                           |
| http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz            |
|                                                                       |
| Downloading                                                           |
| http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz            |
|                                                                       |
| Processing\...                                                        |
|                                                                       |
| Done!                                                                 |
|                                                                       |
| Train Epoch: 1 \[0/60000 (0%)\] Loss: 2.305400                        |
|                                                                       |
| Train Epoch: 1 \[640/60000 (1%)\] Loss: 1.359776                      |
|                                                                       |
| Train Epoch: 1 \[1280/60000 (2%)\] Loss: 0.842885                     |
|                                                                       |
| Train Epoch: 1 \[1920/60000 (3%)\] Loss: 0.587047                     |
|                                                                       |
| Train Epoch: 1 \[2560/60000 (4%)\] Loss: 0.368678                     |
|                                                                       |
| Train Epoch: 1 \[3200/60000 (5%)\] Loss: 0.468111                     |
|                                                                       |
| Train Epoch: 1 \[3840/60000 (6%)\] Loss: 0.263467                     |
+=======================================================================+
+-----------------------------------------------------------------------+

Figure 13 Successful AI/ML application run (PyTorch) with TKG cluster
leveraging NVIDIA GPUs over the network

![Graphical user interface, line chart Description automatically
generated](media/image14.png){width="6.263888888888889in"
height="1.6861111111111111in"}

Figure 14 Bitfusion console during AI/ML application run (PyTorch) in
TKG Workload Cluster ("cluster-01")

# Summary

Our PoC has successfully validated access to Bitfusion served GPUs in
the GPU Server Farm from a containerized AI/ML sample application
running on Kubernetes platform on different Compute Cluster. This also
means the AI/ML application runs with PyTorch were executed successfully
with GPU access over the network through Bitfusion.

In summary, the solution with VMware and Dell Technologies used for our
PoC in Phase I has effectively shown the following:

a.  Native Support for Kubernetes with VMware vSphere with Tanzu

b.  Bitfusion which enables sharing of GPUs across the network (Compute
    Cluster and GPU Server Farm)

c.  Integration between Kubernetes and Bitfusion

d.  Bitfusion can be used over the network from Kubernetes for machine
    learning

In the upcoming Phase II PoC, we will focus on our partner's full suite
of AI/ML product deployment on the same PoC environment, with additional
integration of NFS, API management and Identity Management etc.

# References

-   [Bitfusion
    Documentation](https://docs.vmware.com/en/VMware-vSphere-Bitfusion/index.html)

-   [VMware Kubernetes Integration with
    Bitfusion](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/solutions/vmw-kubernetes-bitfusion-integration.pdf)

-   [VMware vSphere with Bitfusion on Dell EMC PowerEdge
    Servers](https://downloads.dell.com/manuals/all-products/esuprt_software_int/esuprt_software_virtualization_solutions/vmware-esxi-7x_deployment-guide_en-us.pdf)

-   [Machine Learning on vSphere: Choosing A Best Method for GPU
    Deployment with
    VMs](https://blogs.vmware.com/apps/2020/04/machine-learning-on-vsphere-choosing-a-best-method-for-gpu-deployment-with-vms.html)

-   [VMware Kubernetes integrates and works seamlessly with vSphere
    Bitfusion](https://blogs.vmware.com/apps/2020/07/vmware-kubernetes-integrates-and-works-seamlessly-with-vsphere-bitfusion-part-1-of-2.html)

***Previous**:*

-   Introduction

-   Solution Overview & Architecture
# tkinsoon.github.io
