A container runtime is needed prior to creating a K8 cluster.
During the cluster creation process, the k8 cluster automatically detects an installed container runtime
by scanning through the Unix domain sockets, if there are any whitin your local machine.
The unix domain socket uses TCP as the underlying transport protocol.
It is used for bidirectional data communication happening on the same OS

A container is an isolated process on the kernel.
A cli client talks to the runtime that tells the kernel "give me all of this processes that i need to create in order to run a container"
Kernel creates those processes called containers.
The process isolation we need from containers are given by features in the linux kernel.

- Linux kernel namespace != k8s namespace. Resources in the same namespace see same things
  - pid: in != namespaces use the same pid
  - net: ports in the containers don't interfere with ports open in the kernel
  - mnt: mount filesystems on containers without conflicting the host's filesystem
- cgroups - resource limits
- seccom-bff

lxc = creating those namespaces and processes isolation
containerd = APIs additional functionality, pull/push images, unpack and pass them to lower level runtime

A container runtime is responsible for setting up namespaces and cgroups and then running commands inside those namespaces.

CRI (Container Runtime Interface) enables kubelet to use a wide variety of containers runtimes without the need recompile if runtime changes.
Allows any vendor to work as container runtime for k8s as long as they comply to OCI standards.

OCI: designs standards for OS-level virtualizarion (linux containers) - imagespec: how an image should be built - runtimespec: how any container runtime should be developed
With this specs anyone can build a container runtime to work with k8s.

CRI-O= a container runtime

![](https://kubernetes.io/images/blog/2018-05-24-kubernetes-containerd-integration-goes-ga/containerd.png))

Docker's scope is too large for a K8s cluster. It has CLI, API, build tools, volume, auth security + the containerd (daemon) runtime

If anyone hacks your node, Docker being installed could build an image and replace an image node with that. With containerd you can't build images in the node
so how to debug? if you have a problem with a container? You can use the crictl (conteinerd client) to troubleshoot containers and pods on nodes in your cluster in an emergency.
CTR create and manage containers with containerd.

In order to allow k8s to use containerd (using CRI interface) configure the service to point to the containerd socket,

v1.24 remove dockershim
All the images built before docker was removed continue to work because it followed the imagespec standard.

containerd comes with a CLI ctr limited (for debugging) "ctr images pull" "ctr run ...."

A better alternative, nerdctl CLI similar to docker.

crictl CLI for CRI compatible container runtimes. Installed separately for can connect with any cri runtime container set the right endpoint if you have multiple CRI configured.

https://github.com/containerd/nerdctl?tab=readme-ov-file#macos

```
brew install lima
limactl start
lima nerdctl run -d --name nginx -p 127.0.0.1:8080:80 nginx:alpine
```
