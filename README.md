# Raspberry Pi Kubernetes (K8s) Cluster

## Project Overview

This project documents the process of building a 6-node Kubernetes cluster using various models of Raspberry Pi. The goal was to create a functional, scalable, and redundant environment for deploying containerized applications and services, all while utilizing existing hardware. This serves as a practical exploration into container orchestration, network configuration, and system administration in a distributed environment.

## Key Features

*   **6-Node Cluster:** Provides high availability and load balancing for deployed services.
*   **Mixed Raspberry Pi Models:** Demonstrates compatibility across different Raspberry Pi versions (2B, 2B+, 3B, 3B+).
*   **Centralized Power:** Utilizes a single multi-port USB hub to power all nodes, simplifying the physical setup.

(images/k8s-cluster-assembled.jpg)


## Technical Specifications

### Hardware

*   (6) Raspberry Pi SBCs (Models: 2B, 2B+, 3B, 3B+)
*   (1) 6-Node "Bramble" Case by C4Labs
*   (1) Kamera 10-Port 120W USB Charging Hub
*   (6) Micro USB Cables
*   (6) Ethernet Cables
*   (6) SD Cards (with Raspberry Pi OS installed)

(images/k8s-cluster-pis-before.jpg)

### Software & Networking

*   **Operating System:** Raspberry Pi OS
*   **Container Orchestration:** MicroK8s
*   **Networking:** Static IP addresses and a custom /etc/hosts file for hostname resolution.


(images/k8s-cluster-powered.jpg)

## Cluster Setup and Configuration

### Step 1: Hardware Assembly and OS Preparation

The Raspberry Pis were assembled into the 6-node Bramble case. Each Pi was equipped with an SD card flashed with a fresh installation of Raspberry Pi OS.

### Step 2: Network Configuration

Before booting the cluster, each node was configured with a static IP address. A custom `/etc/hosts` file was created and distributed to all nodes to enable simple and reliable communication between them using hostnames (e.g., `pi-1`, `pi-2`).

```
# Example /etc/hosts configuration:
127.0.0.1 localhost localhost.local
10.0.0.21 pi-1 pi-1.local
10.0.0.22 pi-2 pi-2.local
10.0.0.23 pi-3 pi-3.local
10.0.0.24 pi-4 pi-4.local
10.0.0.25 pi-5 pi-5.local
10.0.0.26 pi-6 pi-6.local
```

### Step 3: Enable C-Groups

To ensure the Kubelet functions correctly, c-groups were enabled on each Raspberry Pi by modifying the `/boot/firmware/cmdline.txt` file and adding the following boot options:

```
cgroup_enable=memory cgroup_memory=1
```

After modifying the file, each node was rebooted.

### Step 4: Install MicroK8s

MicroK8s was installed on all 6 nodes using the command:

```bash
sudo snap install microk8s --classic
```

### Step 5: Cluster Formation

One node (`pi-1`) was designated as the master node. The following command was run on the master to generate a join token for the other nodes:

```bash
sudo microk8s.add-node
```

### Step 6: Join Worker Nodes

On each of the remaining 5 nodes, the `microk8s.join` command provided by the master was executed to join them to the cluster.

### Step 7: Verify Cluster Status

After a few minutes, the cluster status was verified from the master node using `kubectl`. The output confirmed that all 6 nodes were in a `Ready` state.

```bash
microk8s.kubectl get node
```

**Output:**

```
NAME   STATUS   ROLES    AGE   VERSION
pi-1   Ready    master   1h    v1.14.6+888f9c630
pi-2   Ready    <none>   1h    v1.14.6+888f9c630
pi-3   Ready    <none>   1h    v1.14.6+888f9c630
pi-4   Ready    <none>   1h    v1.14.6+888f9c630
pi-5   Ready    <none>   1h    v1.14.6+888f9c630
pi-6   Ready    <none>   1h    v1.14.6+888f9c630
```

## Conclusion

This project successfully demonstrates the creation of a fully functional Kubernetes cluster on low-power Raspberry Pi hardware. It serves as a testament to the versatility of single-board computers and the power of container orchestration. The cluster is now ready for deploying and managing containerized applications in a high-availability environment.
