```
This repository contains my own multi-arch build methods for building
Kubernetes and the default CNI plugins from source. It was created because I
wanted to run K8s on unsupported architectures, as well as have a multi-arch
K8s cluster. While my research seems to indicate that everybody knows this is
possible, and Docker has been built for almost every architecture, the ease and
availability of running K8s across the spectrum it is rather lacking, so I set
out to do it myself. In particular, I have like 10 Pi Zeros, and even with full
production Docker and Kubelet and Kube-proxy, they still have at least 50-100
megs of RAM free to do... something. Additionally, building K8s through their
provided Docker build process produces binaries still dynamically linked to
libc. On top of that, the arm versions don't support arm6. So, here we are.

This repo will be updated with more arches and more information as I have time.
Right now only the standard arches supported by Kubernetes by default are
available -- except my arm version works on both arm6 and arm7, and all my
binaries are 100% static and stripped. In the future I plan to add several more
arches, including MIPS and RISCV.  (Also, as of right now s390x builds aren't
working...)

Additionally, I intend to one day make these binary files into containers that
can be drop-in replacement images for use with kubeadm. That will require
creating etcd and coredns containers as well, which I haven't done yet. (The
goal and purpose of this repo is for me to have a reliable method of creating
both static binaries and kubeadm replacement images for a wider range of
architectures than is officially supported.)

Images are built on the arches natively in QEMU containers, using the buildx
plugin and my host's QEMU virtualization.  If you want to build the images
yourself from the provided Dockerfiles, you will need to get QEMU working with
Docker, which is an entirely different hassle that you can google about.

This repository is not really supposed to be used by anybody. The nodes and
build processes are for my own reference. I only put this up online because I
wanted people who see these binaries on Docker Hub to have confidence in them
and be able to recreate them, for their own security and peace of mind. So I'm
not providing a build script or anything. Please don't post issues, they likely
won't be responded to. This repo is solely for informational purposes. The
readme's and Dockerfiles should suffice if you want to build the yourself. Just
make sure you have buildx working with QEMU and your target architecture and
have a build context set.  My compiled versions can be found at:

https://hub.docker.com/r/maxweiss/k8s-multiarch-bins

Those containers can be used either to grab a binary FROM for a multi-stage
build, or you can just mount the folders on the host to grab the binaries for
your own use. All they do is hold the binaries. They don't run anything.
```