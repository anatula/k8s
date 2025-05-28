To remove old keys after recreting with `vagrant up`:

`ssh-keygen -R 192.168.56.5`

Edit (or create) `~/.ssh/config` and add:
```
bash
Host jumpbox
    HostName 192.168.56.5
    User root
```
REMEMBER password is `root`

`ssh root@jumpbox`

## K8s architecture

![Kubernetes Components](https://kubernetes.io/images/docs/components-of-kubernetes.svg "Kubernetes Architecture")


### [Prerequisites](https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/01-prerequisites.md)

Covered by the vagrant file, do `vagrant up`

### [Setting up the Jumpbox](https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/02-jumpbox.md)

Follow instructions

### [Provisioning Compute Resources](https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/03-compute-resources.md)

For our vagrant setup:

`vim machines.txt` with:

```
192.168.56.10 server.kubernetes.local server
192.168.56.11 node-0.kubernetes.local node-0 10.200.0.0/24
192.168.56.12 node-1.kubernetes.local node-1 10.200.1.0/24
```

Note: ssh root access for the machines is already done by Vagrant file

Follow instructions to generate and distrute ssh keys, assign hostnames and: 

- Host Lookup Table - Maps VM names (jumpbox, server, node-0) to IPs so you don’t have to remember raw addresses.

- Local /etc/hosts - Lets your laptop SSH into VMs using names (e.g., ssh server) instead of typing IPs repeatedly.

- Remote /etc/hosts - Ensures Kubernetes nodes can find each other by hostname (e.g., node-0 → server), required for cluster networking.

### [Provisioning the CA and Generating TLS Certificates](https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/04-certificate-authority.md)


### **Summary of Kubernetes Certificates in "The Hard Way" Lab**  

#### **1. Client Certificates** (Used for authentication to the API server)  
- **`admin.crt`** → Cluster admin (human operator)  
- **`kube-controller-manager.crt`** → Controller Manager component  
- **`kube-scheduler.crt`** → Scheduler component  
- **`kube-proxy.crt`** → Kube-Proxy service (used in kubeconfig, not directly copied to nodes)  
- **`node-0.crt` & `node-1.crt`** → Kubelet authentication (renamed to `kubelet.crt` on nodes)  

#### **2. Server Machine Certificates** (For API server and security)  
- **`ca.key` & `ca.crt`** → Root Certificate Authority (used to sign all certs)  
- **`kube-api-server.key` & `kube-api-server.crt`** → API Server TLS termination  
- **`service-accounts.key` & `service-accounts.crt`** → Service Account token signing  

#### **3. Node Machine Certificates** (Copied to `node-0` & `node-1`)  
- **`ca.crt`** → Trusted CA certificate (to verify API server)  
- **`node-X.crt` → Renamed to `kubelet.crt`** → Kubelet identity (authenticates node to API server)  
- **`node-X.key` → Renamed to `kubelet.key`** → Private key for kubelet  

#### **4. Key Notes**  
- **`kube-proxy.crt` is not copied to nodes** → Used later in kubeconfig setup.  
- **Node certs (`node-0.crt/node-1.crt`) become `kubelet.crt`** → Standardized name for kubelet auth.  
- **Each component has its own cert** → Ensures least privilege & secure mutual TLS (mTLS).  

This setup ensures secure communication between all Kubernetes components while following PKI best practices. 🚀

### Generating Kubernetes Configuration Files for Authentication


Secure authentication for **users** and **components** to access the Kubernetes cluster
- Each kubeconfig contains:
  - **Cluster info** (API server endpoint + CA certificate)
  - **User credentials** (client certificate + private key)
  - **Context** (links user identity to target cluster)

#### 2. Key Configuration Files

| Component               | API Server Communication | Authentication Purpose                  | Critical Function                     |
|-------------------------|--------------------------|-----------------------------------------|---------------------------------------|
| **admin.kubeconfig**    | Continuous               | Human administrator access              | `kubectl` command execution           |
| **node-0/1.kubeconfig** | Continuous               | Kubelet authentication                  | Node↔API server communication         |
| **controller-manager**  | Continuous               | Cluster state management                | Maintaining replicas/node health      |
| **scheduler.kubeconfig**| Continuous               | Pod scheduling decisions                | Assigning pods to nodes               |
| **kube-proxy.kubeconfig**| *Initial sync only*      | Service/Endpoint discovery              | Node-local network rule management    |

#### 3. API Server Communication Patterns
- **Continuous Communication** (most components):
  - Kubelet: Constant node status updates
  - Controller Manager: Continuous state reconciliation
  - Scheduler: Regular scheduling decisions
  - Admin: On-demand `kubectl` commands

- **Initial Sync Only** (special case):
  - `kube-proxy`: Contacts API server once at startup to get Services/Endpoints, then operates locally using iptables/IPVS rules

#### 4. Security Design Principles
1. **Component Isolation**: Each has unique identity (e.g., `system:node:node-0`)
2. **Least Privilege**: Strict RBAC permissions tied to each certificate
3. **Separation of Concerns**:
   - Control Plane (API-interactive components)
   - Data Plane (`kube-proxy`'s node-local operations)

#### 5. Why This Architecture Matters
- **Security**: Mutual TLS (mTLS) for all API communications
- **Scalability**: Node-local proxies reduce API server load
- **Reliability**: Clear component boundaries prevent cascading failures
- **Auditability**: Distinct identities enable precise access tracing

> **Key Insight**: While most Kubernetes components maintain continuous API server dialogue for cluster coordination, `kube-proxy` uniquely transitions to offline operation after initial configuration - a critical optimization for network performance.

### Generating the Data Encryption Config and Key
Follow instructions

### Bootstrapping the etcd Cluster
Follow instructions
### Bootstrapping the Kubernetes Control Plane
Follow instructions
### Bootstrapping the Kubernetes Worker Nodes
Follow instructions
### Configuring kubectl for Remote Access
Follow instructions
### Provisioning Pod Network Routes
Follow instructions
### Smoke Test
Follow instructions
### Cleaning Up
To clean up do `vagrant destroy -f`