# Platform linux/arm64/v8


# golang:alpine  using alpine version 3.12 for arch linux/arm64/v8
FROM docker.io/library/golang@sha256:5d3cb2386461b0291a9fb11dd78bc44d35c3fab33de025083008ecb717253bc0

ARG K8S_VERSION="1.18.3"
ARG CNI_PLUGIN_VER_TAG="v0.8.6"
ARG MUSL_VERSION="1.1.24"
# Need to change label on the bottom image label too
LABEL org.label-schema.version=$K8S_VERSION

ARG BUILD_DATE="1970-01-01T00:00:00+0000"
LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.schema-version="1.0"
LABEL org.label-schema.maintainer="TWF4IFdlaXNzIDxtYXhAbWF4d2Vpc3MuaW8+"
LABEL org.label-schema.name="k8s-multiarch-builder"
LABEL org.label-schema.description="Kubernetes and CNI Plugin Multi-arch Build"
LABEL org.label-schema.url="https://github.com/maxweisspoker/k8s-multiarch-dockerfiles"

RUN apk add --update-cache build-base musl-dev musl-utils tar bash rsync git conntrack-tools iptables-dev ipset socat \
    && wget https://www.musl-libc.org/releases/musl-${MUSL_VERSION}.tar.gz \
    && tar -xvf musl-${MUSL_VERSION}.tar.gz && cd musl-${MUSL_VERSION} && ./configure && make && make install \
    && cd .. && rm -f musl-${MUSL_VERSION}.tar.gz && rm -Rf musl-${MUSL_VERSION} && rm -Rf musl.${MUSL_VERSION} \
    && rm -rf /var/cache/apk/*

RUN mkdir -p /go/src/k8s.io/ && cd /go/src/k8s.io/ \
    && git clone https://github.com/kubernetes/kubernetes \
    && cd /go/src/k8s.io/kubernetes \
    && git checkout "v${K8S_VERSION}"

ENV LDFLAGS='-d -s -w -linkmode=external -extldflags="-static" -static -static-libgcc'
ENV CGO_ENABLED=0
ENV GOFLAGS=""

# Run commands separated, so you can save/cache each successful build.
# Since this is a builder image that will be discarded anyway, any extra space
# used shouldn't matter for people wanting to build all at once.
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kubelet
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kube-proxy
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kube-apiserver
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kube-controller-manager
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kubeadm
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kubectl
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/kube-scheduler
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=cmd/cloud-controller-manager
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=vendor/k8s.io/kube-aggregator
RUN cd /go/src/k8s.io/kubernetes/ && make WHAT=vendor/k8s.io/apiextensions-apiserver
RUN cd /go/src/k8s.io/kubernetes/ && chmod 755 /go/src/k8s.io/kubernetes/_output/bin/*

RUN cd /go/src/k8s.io/kubernetes/build/pause/ \
    && gcc -Os -Wall -Werror -static -s -g -fPIE -fPIC -o pause pause.c \
    && chmod 755 /go/src/k8s.io/kubernetes/build/pause/pause

RUN mkdir -p /go/src/github.com/containernetworking/ && cd /go/src/github.com/containernetworking/ \
    && git clone https://github.com/containernetworking/plugins \
    && cd /go/src/github.com/containernetworking/plugins/ \
    && git checkout "$CNI_PLUGIN_VER_TAG" \
    && sed -i 's/\$GO\ build -o \"\${PWD}\/bin\/\$plugin\" \"\$\@\"/\$GO\ build -o \"\$\{PWD\}\/bin\/\$plugin\" \"\$\@\"\ \-ldflags\=\"\-d\ \-s\ \-w\"/' build_linux.sh \
    && bash ./build_linux.sh \
    && chmod 755 /go/src/github.com/containernetworking/plugins/bin/*

WORKDIR /go/
CMD /bin/bash


# alpine 3.12.0 for linux/arm64/v8
FROM docker.io/library/alpine@sha256:3b3f647d2d99cac772ed64c4791e5d9b750dd5fe0b25db653ec4976f7b72837c

ARG K8S_VERSION="1.18.3"
LABEL org.label-schema.version=$K8S_VERSION

ARG BUILD_DATE="1970-01-01T00:00:00+0000"
LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.maintainer="TWF4IFdlaXNzIDxtYXhAbWF4d2Vpc3MuaW8+"
LABEL org.label-schema.name="k8s-multiarch-bins"
LABEL org.label-schema.description="Kubernetes and CNI Plugin Multi-arch Binaries"
LABEL org.label-schema.url="https://github.com/maxweisspoker/k8s-multiarch-dockerfiles"

RUN mkdir -p /k8s-bins/ && mkdir -p /cni-plugin-bins/

COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kube-proxy /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kube-apiserver /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kube-controller-manager /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kubelet /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kubeadm /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kubectl /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kube-scheduler /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/cloud-controller-manager /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/kube-aggregator /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/_output/bin/apiextensions-apiserver /k8s-bins/
COPY --from=0 /go/src/k8s.io/kubernetes/build/pause/pause /k8s-bins/

COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/bandwidth /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/firewall /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/flannel /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/portmap /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/sbr /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/tuning /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/bridge /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/host-device /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/ipvlan /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/loopback /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/macvlan /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/ptp /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/vlan /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/dhcp /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/host-local /cni-plugin-bins/
COPY --from=0 /go/src/github.com/containernetworking/plugins/bin/static /cni-plugin-bins/

RUN chmod 755 /k8s-bins/* && chmod 755 /cni-plugin-bins/*

WORKDIR /
CMD /bin/sh

