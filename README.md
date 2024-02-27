## 1. Introduction

Since the author of the ["vromero"](https://github.com/vromero/activemq-artemis-docker) image - well done to him - stopped maintaining it at v2.16 I forked it here and will release new Artemis versions at [Docker Hub](https://hub.docker.com/r/qoricode/activemq-artemis) as they are released on "reasonable endeavours". Note I'm not maintaining the Alpine images and have switched the Debian Images to Debian Slim. 

There are many convenient features such as merging snippets of configuration which are easier to manage than a monolithic broker.xml file, and simple switches to enable JSON logging or the Prometheus agent for example. For usage information see below.

## 2. Usage

### 2.1 Breaking Artemis Changes

Check the [Artemis release log](https://activemq.apache.org/components/artemis/documentation/latest/versions.html) for possible breaking changes which could impact your Artemis configuration and/or client code.

### 2.2 General Usage

Prebuilt docker images from this project are available at Docker Hub as noted above but in order to use them, see the general usage
instructions of the parent project [here](https://github.com/vromero/activemq-artemis-docker/blob/master/README.md), noting the changes detailed below.

Note that since v2.30 the Artemis maintainers have been publishing official images to Docker Hub too - see [here](https://activemq.apache.org/components/artemis/documentation/latest/docker.html).

### 2.3 Docker Build Changes Since v2.16

Fixed a few minor issues (from my point of view) - 
- Old 2016 version of ca-certificates was baked in - now pulls latest.
- Internet fetch operations were muted during the build - now reports e.g. certificate issues during build and pulls artemis signing keys via https not http.
- ENABLE_JMX and ENABLE_JMX_EXPORTER would be enabled on any value e.g. false, now will only be enabled if == true.
- ENABLE_JMX_EXPORTER would activate ENABLE_JMX when not actually needed - now independent of each other.
    - Note ENABLE_JMX enables *remote* JMX access, anonymous by default, and hence should be avoided in production, certainly without also configuring RBAC. Local JMX required by the exporter is enabled via broker.xml and is on by default. Using the exporter is a safer option for monitoring.

Other differences:
- The default build removes curl from Debian to avoid potential vulnerabilities.
- The Makefile supports verbose logging and clean builds as simple options - see [BUILD.md](https://github.com/qoricode/activemq-artemis-docker/blob/master/BUILD.md).
- [tags.csv](https://github.com/qoricode/activemq-artemis-docker/blob/master/tags.csv) now separates the JRE and Linux OS base images. The JRE is overlaid on the OS image. Also dependency versions are now configured here instead of being hardwired in the Dockerfile.

## 3. Running in Orchestrators

At the moment only Docker is directly targeted by this image and additional work is needed to use with e.g. Docker Compose or Kubernetes with clustering enabled for example.

### 3.1 Running in Kubernetes

ActiveMQ Artemis can leverage [JGroups](http://www.jgroups.org/) to discover the members of the cluster. This is documented [here](https://activemq.apache.org/components/artemis/documentation/latest/clusters.html). UDP discovery will not work reliably if at all on Kubernetes networks and therefore JGroups is required.
A plugin called [jgroups-kubernetes](https://github.com/jgroups-extras/jgroups-kubernetes) allows JGroups to discover using Kubernetes.
Both KUBE_PING (via Kubernetes API) and DNS_PING (using the SRV records of a [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#srv-records)) 
building blocks are included to facilitate initial membership discovery.

[jgroups-kubernetes](https://github.com/jgroups-extras/jgroups-kubernetes/tree/2.0.1.Final) version `2.0.1.Final` is included in the
classpath of this image, however everything about the configuration of jgroups and jgroups-kubernetes is left to the user.

A complete alternative to this docker image for Kubernetes is the [ArtemisCloud Operator](https://artemiscloud.io/) maintained by Red Hat which has some nice features, for example YAML broker configuration, CRDs for common tasks, and when scaling down it will transfer messages to a live broker, but is somewhat let down by the breadth & depth 
of the (public) documentation and you will have to dig into the code to understand many of the options. It can at least be used to understand how best to configure broker clusters in k8s by examining the XML configuration in the running containers.

## 4. User Feedback

### 4.1 Issues

If you have any problems with or questions about this image, please raise them via [GitHub discussions](https://github.com/qoricode/activemq-artemis-docker/discussions).

### 4.2 Contributing

You are welcome to contribute new features, fixes, or updates, large or small.

Before you start to code, please [discuss your plans first](https://github.com/qoricode/activemq-artemis-docker/discussions), especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.

