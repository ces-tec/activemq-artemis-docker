## 1. Introduction

Since the author of the ["vromero"](https://github.com/vromero/activemq-artemis-docker) image - well done to him - stopped maintaining it at v2.16 I forked it here and will release new Artemis versions at [Docker Hub](https://hub.docker.com/r/qoricode/activemq-artemis) as they are released on "reasonable endeavours". Note I'm not maintaining the Alpine images and have switched the Debian Images to Debian Slim.

Also fixed a few minor issues (from my point of view) - 
- Old 2016 version of ca-certificates was baked in - now pulls latest.
- Internet fetch operations were muted during the build - now reports e.g. certificate issues during build and pulls artemis signing keys via https.
- ENABLE_JMX and ENABLE_JMX_EXPORTER would be enabled on any value e.g. false, now will only be enabled if == true.
- ENABLE_JMX_EXPORTER would activate ENABLE_JMX when not actually needed - now independent of each other.
    - Note ENABLE_JMX actually enables remote, anonymous JMX access. (Local) JMX is enabled via broker.xml and on by default.

General usage instructions are the same as the parent except where noted - see [here](https://github.com/vromero/activemq-artemis-docker/blob/master/README.md).

Other differences:
- The default build now removes curl from Debian to avoid potential vulnerabilities.
- The Makefile supports verbose logging and clean builds as simple options - see [BUILD.md](https://github.com/qoricode/activemq-artemis-docker/blob/master/BUILD.md).
- [tags.csv](https://github.com/qoricode/activemq-artemis-docker/blob/master/tags.csv) now separates the JRE and Linux OS base images. The JRE is overlaid on the OS image. Also dependency versions are now configured here instead of being hardwired in the Dockerfile.

## 2. Running in orchestrators

At the moment only docker is directly supported for this image.

## 3. Running in Kubernetes

ActiveMQ Artemis can leverage [JGroups](http://www.jgroups.org/) to discover the members of the cluster. This is documented [here](https://activemq.apache.org/components/artemis/documentation/latest/clusters.html). 
A plugin called [jgroups-kubernetes](https://github.com/jgroups-extras/jgroups-kubernetes) allows JGroups to discover using Kubernetes.
Both KUBE_PING (via Kubernetes API) and DNS_PING (using the SRV records of a [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#srv-records)) 
building blocks are included to facilitate initial membership discovery.

[jgroups-kubernetes](https://github.com/jgroups-extras/jgroups-kubernetes/tree/2.0.0.Final) version `2.0.0.Final` is included in the
classpath of this image, however everything about the configuration of jgroups and jgroups-kubernetes is left to the user.

An alternative option for Kubernetes is the [ArtemisCloud Operator](https://artemiscloud.io/) maintained by Red Hat which is quite 
full-featured (for example when scaling down it will transfer messages to a live broker) but somewhat let down by the depth of the
(public) documentation and you will have to dig into the code to understand many of the options.

## 4. User Feedback

### 4.1 Issues

If you have any problems with or questions about this image, please contact us through [GitHub discussions](https://github.com/qoricode/activemq-artemis-docker/discussions).

### 4.2 Contributing

You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend [discussing your plans first](https://github.com/qoricode/activemq-artemis-docker/discussions), especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.
