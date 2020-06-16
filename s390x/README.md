TODO.

For the life of me, I cannot get a static binaries for everything. Some things get compiled statically, some still link libc. I've spent days trying. No amount of build flags, different libc providers, nor different environments is helping. Even using the stock K8s build environment in a VM produces non-static builds for some of the bins. While that may be acceptable for most use-cases, the whole point of this repo is to have 100% static binaries.  If and when I figure it out, this section and my Docker Hub will be updated.

If anybody has a Dockerfile that will produce 100% static, stripped binaries for all K8s components and CNI plugins which are available in my other builds, please do give me a pull request.
