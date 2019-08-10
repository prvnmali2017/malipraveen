+++
date = "2019-08-08"
title = "Jenkins on Kubernetes Engine (GCE)"
slug = ""
tags = [
    "Kubernetes",
    "tools",
    "Cloud"
]
categories = []
series = []
+++

<div align=center><a href='https://www.counter12.com'><img src='https://www.counter12.com/img-xAzZ1zDDd4ZzBwZZ-50.gif' border='0' alt='contador de acesso grátis'></a><script type='text/javascript' src='https://www.counter12.com/ad.js?id=xAzZ1zDDd4ZzBwZZ'></script></div>

Jenkins on Kubernetes Engine
===========================

This topic teaches you best practices for using Jenkins with Google Kubernetes Engine. To implement this solution, see[setting up Jenkins on Kubernetes Engine](https://cloud.google.com/solutions/jenkins-on-kubernetes-engine-tutorial).

Jenkins is an open-source automation server that lets you flexibly orchestrate your build, test, and deployment pipelines. Kubernetes Engine is a hosted version of Kubernetes, a powerful cluster manager and orchestration system for containers.

When you need to set up a continuous delivery (CD) pipeline, deploying Jenkins on Kubernetes Engine provides important benefits over a standard VM-based deployment:

-   When your build process uses containers, one virtual host can run jobs against different operating systems.

-   Kubernetes Engine provides ephemeral build executors, allowing each build to run in a clean environment that's identical to the builds before it.

-   As part of the ephemerality of the build executors, the Kubernetes Engine cluster is only utilized when builds are actively running, leaving resources available for other cluster tasks such as batch processing jobs.

-   Build executors launch in seconds.

-   Kubernetes Engine leverages the [Google global load balancer](http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44824.pdf) to route web traffic to your instance. The load balancer handles SSL termination, and provides a global IP address that routes users to your web front end on one of the fastest paths from the point of presence closest to your users through the Google backbone network.

For a deep dive into Jenkins on Kubernetes Engine, watch the Next 2018 talk on YouTube:

Deploying the Jenkins master with Helm
--------------------------------------

Use [Helm](https://github.com/kubernetes/helm) to deploy Jenkins from the [Charts](https://github.com/kubernetes/charts) repository. Helm is a package manager you can use to configure and deploy[Kubernetes apps](https://hub.kubeapps.com/).

For guidance on how to configure Jenkins for Kubernetes Engine, see [Configuring Jenkins for Kubernetes Engine](https://cloud.google.com/solutions/configuring-jenkins-kubernetes-engine).

The following image describes the architecture for deploying Jenkins in a multi-node Kubernetes cluster.

![Jenkins and Kubernetes architecture](https://cloud.google.com/solutions/images/jenkins-kubernetes-architecture.svg)

Deploy the Jenkins master into a separate [namespace](http://kubernetes.io/docs/admin/namespaces/) in the Kubernetes cluster. Namespaces allow for [creating quotas](http://kubernetes.io/docs/admin/resourcequota/)for the Jenkins deployment as well as logically separating Jenkins from other deployments within the cluster.

### Creating Jenkins services

Jenkins provides two services that the cluster needs access to. Deploy these services separately so they can be individually managed and named.

-   An externally-exposed [NodePort service](http://kubernetes.io/docs/user-guide/services/#type-nodeport) on port 8080 that allows pods and external users to access the Jenkins user interface. This type of service can be load balanced by an HTTP load balancer.

-   An internal, private [ClusterIP service](http://kubernetes.io/docs/user-guide/services/#publishing-services---service-types) on port 50000 that the Jenkins executors use to communicate with the Jenkins master from inside the cluster.

The following sections show sample service definitions.

[jenkins/k8s/service_jenkins.yaml](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/v1/jenkins/k8s/service_jenkins.yaml)

[VIEW ON GITHUB](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/v1/jenkins/k8s/service_jenkins.yaml)

```
 kind: Service\ apiVersion: v1\ metadata:\
 name: jenkins-ui\ namespace: jenkins\ spec:\
 type: NodePort\ selector:\
 app: master\ ports:\
 -  protocol: TCP\ port: 8080\ targetPort: 8080\ name: ui
```
[jenkins/k8s/service_jenkins.yaml](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/v1/jenkins/k8s/service_jenkins.yaml)

[VIEW ON GITHUB](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/v1/jenkins/k8s/service_jenkins.yaml)

```
 kind: Service\ apiVersion: v1\ metadata:\
 name: jenkins-discovery\ namespace: jenkins\ spec:\
 selector:\
 app: master\ ports:\
 -  protocol: TCP\ port: 50000\ targetPort: 50000\ name: slaves
```
### Creating the Jenkins deployment

Deploy the Jenkins master as a [deployment](http://kubernetes.io/docs/user-guide/deployments) with a [replica count](http://kubernetes.io/docs/user-guide/replication-controller/#multiple-replicas) of 1. This ensures that there is a single Jenkins master running in the cluster at all times. If the Jenkins master pod dies or the node that it is running on shuts down, Kubernetes restarts the pod elsewhere in the cluster.

It's important to set [requests and limits](https://github.com/helm/charts/blob/28250ead2088bb36831864f43648d94dfee4f618/stable/jenkins/values.yaml#L42) as part of the Helm deployment, so that the container is guaranteed a certain amount CPU and memory resources inside the cluster before being scheduled. Otherwise, your master could go down due to CPU or memory starvation.

The Jenkins home volume stores XML configuration files and plugin JAR files that make up your configuration. This data is stored on a Persistent Disk managed by the GKE cluster and will persist data across restarts of Jenkins. To change the size of the persistent disk edit the `Persistence.Size` value when installing Jenkins with Helm.

Connecting to Jenkins
---------------------

Once the Jenkins pod has been created you can create a load balancer endpoint to connect to it from outside of Cloud Platform. Consider the following best practices.

-   Use a Kubernetes [ingress](http://kubernetes.io/docs/user-guide/ingress/#what-is-ingress) resource for an easy-to-configure L7 load balancer with SSL termination.

-   Provide SSL certs to the load balancer using Kubernetes secrets. Use `tls.cert` and `tls.key` values, and reference the values in your ingress resource configuration.

Configuring Jenkins
-------------------

### Securing Jenkins

After you connect to Jenkins for the first time, it's important to immediately secure Jenkins. You can follow [the Jenkins standard security setup tutorial](https://wiki.jenkins-ci.org/display/JENKINS/Standard+Security+Setup) for a simple procedure that leverages an internal user database. This setup doesn't require additional infrastructure and provides the ability to lock out anonymous users.

### Installing plugins

You can install the following plugins to enhance the interactions between Jenkins and Kubernetes Engine.

-   The [Kubernetes plugin](https://wiki.jenkins-ci.org/display/JENKINS/Kubernetes+Plugin) enables using Kubernetes service accounts for authentication, and creating labeled executor configurations with different base images. The plugin creates a pod when an executor is required and destroys the pod when a job ends.

-   The [Google Authenticated Source plugin](https://wiki.jenkins-ci.org/display/JENKINS/Google+Source+Plugin) enables using your service account credentials when accessing Cloud Platform services such as Cloud Source Repositories.

To add additional plugins using the Helm chart, edit the [list of plugins](https://github.com/helm/charts/blob/28250ead2088bb36831864f43648d94dfee4f618/stable/jenkins/values.yaml#L137) in the values file that you pass to the Helm install or upgrade commands.

### Customizing the Jenkins Agent Docker image

When creating a pod template, you can either provide an existing Docker image, or you can create a custom image that has most of your build-time dependencies installed. Using a custom image can decrease overall build time and create more consistent build environments.

Your custom Docker image must install and configure the [Jenkins JNLP slave agent](https://github.com/jenkinsci/docker-jnlp-slave). The JNLP agent is software that communicates with the Jenkins master to coordinate running your Jenkins jobs and reporting job status.

One option is to add `FROM jenkins/jnlp-slave` to your image configuration. For example, if your application build process depends on the Go runtime, you can create the following Dockerfile to extend the existing image with your own dependencies and build artifacts.

```
FROM jenkins/jnlp-slave\
RUN apt-get update && apt-get install -y golang\
```

Then, build and upload the image to your project's Container Registry repository by running the following commands.
```
docker build -t gcr.io/[PROJECT]/my-jenkins-image .
gcloud docker -- push gcr.io/[PROJECT]/my-jenkins-image
```
When creating a pod template, you can now set the **Docker image** field to the following string, where `[PROJECT]` is replaced with your project name and `[IMAGE_NAME]` is replaced with the image name.
```
gcr.io/[PROJECT]/[IMAGE_NAME]\
```

The above example ensures that the Go language runtime is pre-installed when your Jenkins job starts.

### Building Docker Images in Jenkins

Cloud Build can be used from within your Jenkins jobs to build Docker images without needing to host your own Docker daemon. Your Jenkins job must have service account credentials available that have been granted the `cloudbuild.builds.editor` role.

For an example Jenkins Pipeline file, see this [GitHub repository](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes/blob/320bf7b11cc3f4f9d74fe26a173f886574e2ef2f/sample-app/Jenkinsfile#L51).

[Kaniko](https://github.com/GoogleContainerTools/kaniko) is another option for users looking to build containers inside their clusters. Kaniko does not require a Docker daemon to be present in order to build and push images to a remote registry.

For an example of using Kaniko in Jenkins, see this [GitHub repository](https://github.com/jenkinsci/kubernetes-plugin/blob/kubernetes-1.14.5/examples/kaniko-declarative.groovy).
