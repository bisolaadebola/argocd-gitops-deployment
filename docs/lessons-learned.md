# Lessons Learned – Argo CD GitOps Deployment

## Skills Practiced

- Provisioning and administering AWS EC2 infrastructure.
- Installing and validating a Kubernetes cluster on Linux.
- Deploying Argo CD into Kubernetes.
- Connecting Argo CD to a GitHub repository.
- Managing Kubernetes manifests through a GitOps workflow.
- Inspecting Deployments, ReplicaSets, Services, and Pods.
- Using Argo CD health and synchronization status to validate deployments.

## Key Takeaways

GitOps provides a version-controlled approach to defining the desired state of applications. Argo CD compares that state with the resources running in Kubernetes and can synchronize differences according to its configuration.

The project also reinforced the importance of validating cluster health, repository paths, Kubernetes manifests, and application status when troubleshooting deployments.

## Challenges and Future Improvements

Further practice will focus on demonstrating a Git change flowing through to the cluster, testing automated synchronization, improving authentication and network security, and exploring a multi-node cluster architecture.

## Outcome

This project strengthened my practical understanding of Kubernetes application management and GitOps using AWS EC2, GitHub, and Argo CD.