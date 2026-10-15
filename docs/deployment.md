# Deployment Guide – Argo CD on AWS EC2

## Prerequisites

- An AWS account with permission to provision EC2 resources.
- An EC2 instance running a compatible Linux distribution.
- Kubernetes and `kubectl` configured on the instance.
- Administrative access to the Kubernetes cluster.
- A GitHub repository containing valid Kubernetes manifests.

## Deployment Process

### 1. Prepare the EC2 Instance

Provision an AWS EC2 instance and connect to it using SSH. Install the required operating-system packages and Kubernetes dependencies.

### 2. Install Kubernetes

Install and configure Kubernetes on the EC2 instance, which serves as both the control-plane and worker node for this single-node cluster.

Verify the cluster status:

```bash
kubectl get nodes -o wide
kubectl get pods -A
```

Confirm that the node is ready before proceeding.

### 3. Install Argo CD

Follow the official [Argo CD Getting Started guide](https://argo-cd.readthedocs.io/en/stable/getting_started/) to install Argo CD in the Kubernetes cluster.

Verify the Argo CD components and Services:

```bash
kubectl get pods -n argocd
kubectl get services -n argocd
```

### 4. Expose Argo CD Using NodePort

By default, the `argocd-server` Service uses the `ClusterIP` type, which provides access within the Kubernetes cluster.

To access the Argo CD dashboard through the EC2 instance, I changed the Service type to `NodePort`.

Run the following command:

```bash
kubectl patch svc argocd-server -n argocd \
  -p '{"spec": {"type": "NodePort"}}'
```

Verify the updated Service configuration:

```bash
kubectl get svc argocd-server -n argocd
```

Confirm that the Service type is `NodePort` and identify the assigned HTTPS NodePort in the `PORT(S)` column.

### 5. Access the Argo CD Dashboard

Use the EC2 instance's public IP address and the assigned HTTPS NodePort to access the dashboard:

```text
https://<EC2-PUBLIC-IP>:<HTTPS-NODEPORT>
```

Replace the placeholders with the actual public IP address and assigned port.

Ensure the EC2 security group permits inbound traffic to that port from your trusted IP address. Complete the Argo CD login using the configured credentials.

**Alternative for local access:** Port forwarding can be used instead of external NodePort access:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then open `https://localhost:8080` in your browser. This requires the command to remain running and access to the local machine where the port-forward is established.

### 6. Connect the GitHub Repository

Connect the GitHub repository containing the Kubernetes manifests to Argo CD.

Create an Argo CD Application and configure:

- **Repository:** The GitHub repository containing the manifests.
- **Path:** `website-workload`
- **Target revision:** `HEAD`
- **Destination:** `in-cluster`
- **Namespace:** `default`

These settings reflect the configuration displayed in the project's Argo CD dashboard.

### 7. Synchronize and Manage the Application

Use the Argo CD interface to inspect the application and synchronize its desired state with the Kubernetes cluster.

Argo CD compares the manifests stored in GitHub with the live resources in Kubernetes. Depending on the application's synchronization settings, changes can be applied manually or automatically.

Verify the deployed resources:

```bash
kubectl get deployments
kubectl get services
kubectl get pods -o wide
```

To list Argo CD Applications, run:

```bash
kubectl get applications -n argocd
```

This command assumes the Argo CD Application custom resource is installed and that the Application exists in the `argocd` namespace.

## Validation

The deployment was validated through the Argo CD dashboard, which displayed:

- **Application health:** Healthy
- **Synchronization status:** Synced
- **Deployment resources:** Deployment and ReplicaSet
- **Networking:** Kubernetes Service
- **Workload status:** Three running Pods, as shown in the application resource tree

These results demonstrate that Argo CD was connected to the GitHub repository and successfully managing the application's Kubernetes resources.

## Security Considerations

- Never commit kubeconfig files, private keys, passwords, or access tokens.
- Restrict EC2 security group rules to trusted IP addresses.
- Avoid exposing the Kubernetes API or Argo CD dashboard publicly without appropriate access controls.
- Use secure authentication and HTTPS when accessing the dashboard.
- Remember that NodePort exposes the Service through a node port; it does not, by itself, configure AWS network access or provide production-grade security.
- Follow the official installation instructions for the Kubernetes distribution and Argo CD version used.

## Further Improvements

- Demonstrate a Git commit triggering a change in the deployed application.
- Test and document automated synchronization.
- Explore declarative Argo CD Application configuration.
- Improve access security and investigate HTTPS ingress or private access methods.
- Extend the environment to a multi-node Kubernetes cluster.