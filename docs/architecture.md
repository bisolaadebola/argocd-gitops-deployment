# Architecture – Argo CD GitOps Deployment

## Overview

This project uses AWS EC2 to host a single-node Kubernetes cluster and Argo CD to manage application resources from a GitHub repository.

The EC2 instance serves as both the Kubernetes control-plane and worker node. Argo CD runs inside the cluster and monitors the configured repository path for Kubernetes manifests.

## Architecture Components

| Component | Purpose |
|---|---|
| AWS EC2 | Hosts the Kubernetes environment |
| Kubernetes | Orchestrates application workloads |
| GitHub | Stores version-controlled Kubernetes manifests |
| Argo CD | Compares Git-defined desired state with live cluster state |
| Deployment | Defines the application workload and replica count |
| ReplicaSet | Maintains the desired number of Pods |
| Service | Provides network access to selected Pods |
| Pods | Run the application containers |

## Workflow

1. Kubernetes manifests are stored in GitHub.
2. Argo CD monitors the configured repository and path.
3. Argo CD compares the desired configuration with live Kubernetes resources.
4. Synchronization applies the desired configuration to the cluster.
5. Argo CD reports the application's health and synchronization status.

## Architecture Considerations

This is a learning environment using a single EC2 instance for both control-plane and worker responsibilities. It demonstrates core Kubernetes and GitOps concepts but does not provide the resilience of a production multi-node cluster.