---
title: "Create Helm-charts, Kustomize overlays from Kubernetes Yamls"
date: 2022-08-04T19:13:10-06:00
draft: false
---

This tutorial will show how to transform a set of Kubernetes YAMLs by parameterizing them by using Move2Kube to generate parameterized Helm charts, Kustomize and Openshift Templates from the Kubernetes YAMLs. Move2Kube can also change the version of Kubernetes resources to target particular clusters.

**Prerequisites**
* Move2Kube CLI tool is installed.
* Use a [kubernetes-to-kubernetes](https://github.com/konveyor/move2kube-demos/tree/main/samples/kubernetes-to-kubernetes) sample. The `kubernetes-to-kubernetes` directory has some Kubernetes YAMLs that deploy a web app with multiple services. There are three services: a frontend website in PHP, a backend API in Node.JS, and a cache service using Redis.

**Procedure**
1. Download the [kubernetes-to-kubernetes](https://github.com/konveyor/move2kube-demos/tree/main/samples/kubernetes-to-kubernetes) sample.

```console
  $ curl https://move2kube.konveyor.io/scripts/download.sh | bash -s -- -d samples/kubernetes-to-kubernetes -r move2kube-demos
  $ ls kubernetes-to-kubernetes/
  api-deployment.yaml  api-service.yaml  redis-deployment.yaml  redis-service.yaml  web-deployment.yaml  web-ingress.yaml  web-service.yaml
```

2. Run `move2kube plan -s kubernetes-to-kubernetes/` to generate a plan file.

```console
$ move2kube plan -s kubernetes-to-kubernetes/
INFO[0000] Configuration loading done
INFO[0000] Planning Transformation - Base Directory
INFO[0000] [ComposeAnalyser] Planning transformation
INFO[0000] [ComposeAnalyser] Done
INFO[0000] [CloudFoundry] Planning transformation
INFO[0000] [CloudFoundry] Done
INFO[0000] [DockerfileDetector] Planning transformation
INFO[0000] [DockerfileDetector] Done
INFO[0000] [Base Directory] Identified 0 named services and 0 to-be-named services
INFO[0000] Transformation planning - Base Directory done
INFO[0000] Planning Transformation - Directory Walk
INFO[0000] Identified 1 named services and 0 to-be-named services in .
INFO[0000] Identified 1 named services and 0 to-be-named services in .
INFO[0000] Transformation planning - Directory Walk done
INFO[0000] [Directory Walk] Identified 1 named services and 1 to-be-named services
INFO[0000] [Named Services] Identified 1 named services
INFO[0000] No of services identified : 1
INFO[0000] Plan can be found at [/home/user/m2k.plan].
```

3. Look at the generated plan file in YAML format.

```console
$ ls
kubernetes-to-kubernetes	m2k.plan
$ cat m2k.plan
```
Notice that Move2Kube has detected all the different services, one for each web app.

```yaml
aapiVersion: move2kube.konveyor.io/v1alpha1
 kind: Plan
 metadata:
   name: myproject
 spec:
   sourceDir: kubernetes-to-kubernetes
   services:
     myproject:
       - transformerName: KubernetesVersionChanger
         type: KubernetesOrgYamlsInSource
         paths:
           KubernetesYamls:
             - .
           ServiceDirPath:
             - .
       - transformerName: Parameterizer
         paths:
           KubernetesYamls:
             - .
           ServiceDirPath:
             - .
   transformers:
     Buildconfig: m2kassets/built-in/transformers/kubernetes/buildconfig/buildconfig.yaml
     CloudFoundry: m2kassets/built-in/transformers/cloudfoundry/cloudfoundry.yaml
     ClusterSelector: m2kassets/built-in/transformers/kubernetes/clusterselector/clusterselector.yaml
     ComposeAnalyser: m2kassets/built-in/transformers/compose/composeanalyser/composeanalyser.yaml
     ComposeGenerator: m2kassets/built-in/transformers/compose/composegenerator/composegenerator.yaml
     ContainerImagesPushScriptGenerator: m2kassets/built-in/transformers/containerimage/containerimagespushscript/containerimagespushscript.yaml
     DockerfileDetector: m2kassets/built-in/transformers/dockerfile/dockerfiledetector/dockerfiledetector.yaml
     DockerfileImageBuildScript: m2kassets/built-in/transformers/dockerfile/dockerimagebuildscript/dockerfilebuildscriptgenerator.yaml
     DockerfileParser: m2kassets/built-in/transformers/dockerfile/dockerfileparser/dockerfileparser.yaml
     DotNetCore-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/dotnetcore/dotnetcore.yaml
     EarAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/earanalyser/ear.yaml
     EarRouter: m2kassets/built-in/transformers/dockerfilegenerator/java/earrouter/earrouter.yaml
     Golang-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/golang/golang.yaml
     Gradle: m2kassets/built-in/transformers/dockerfilegenerator/java/gradle/gradle.yaml
     Jar: m2kassets/built-in/transformers/dockerfilegenerator/java/jar/jar.yaml
     Jboss: m2kassets/built-in/transformers/dockerfilegenerator/java/jboss/jboss.yaml
     Knative: m2kassets/built-in/transformers/kubernetes/knative/knative.yaml
     Kubernetes: m2kassets/built-in/transformers/kubernetes/kubernetes/kubernetes.yaml
     KubernetesVersionChanger: m2kassets/built-in/transformers/kubernetes/kubernetesversionchanger/kubernetesversionchanger.yaml
     Liberty: m2kassets/built-in/transformers/dockerfilegenerator/java/liberty/liberty.yaml
     Maven: m2kassets/built-in/transformers/dockerfilegenerator/java/maven/maven.yaml
     Nodejs-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/nodejs/nodejs.yaml
     PHP-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/php/php.yaml
     Parameterizer: m2kassets/built-in/transformers/kubernetes/parameterizer/parameterizer.yaml
     Python-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/python/python.yaml
     ReadMeGenerator: m2kassets/built-in/transformers/readmegenerator/readmegenerator.yaml
     Ruby-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/ruby/ruby.yaml
     Rust-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/rust/rust.yaml
     Tekton: m2kassets/built-in/transformers/kubernetes/tekton/tekton.yaml
     Tomcat: m2kassets/built-in/transformers/dockerfilegenerator/java/tomcat/tomcat.yaml
     WarAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/waranalyser/war.yaml
     WarRouter: m2kassets/built-in/transformers/dockerfilegenerator/java/warrouter/warrouter.yaml
     WinConsoleApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winconsole/winconsole.yaml
     WinSLWebApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winsilverlightweb/winsilverlightweb.yaml
     WinWebApp-Dockerfile: m2kassets/built-in/transformers/dockerfilegenerator/windows/winweb/winweb.yaml
     ZuulAnalyser: m2kassets/built-in/transformers/dockerfilegenerator/java/zuul/zuulanalyser.yaml
```

4. Run the transformation using `move2kube transform`.

```console
$ move2kube transform
INFO[0000] Detected a plan file at path /home/user/m2k.plan. Will transform using this plan.
? Select all transformer types that you are interested in:
ID: move2kube.transformers.types
Hints:
[Services that don't support any of the transformer types you are interested in will be ignored.]
 ComposeAnalyser, PHP-Dockerfile, ReadMeGenerator, Ruby-Dockerfile, Tekton, Buildconfig, Golang-Dockerfile, Jar, Knative, Nodejs-Dockerfile, Parameterizer, CloudFoundry, DockerfileDetector, Kubernetes, Maven, WinWebApp-Dockerfile, Gradle, KubernetesVersionChanger, WarAnalyser, Rust-Dockerfile, WarRouter, ZuulAnalyser, DotNetCore-Dockerfile,EarRouter, Liberty, Python-Dockerfile, Tomcat, ContainerImagesPushScriptGenerator, DockerfileImageBuildScript, DockerfileParser, ClusterSelector, ComposeGenerator,   EarAnalyser, Jboss, WinConsoleApp-Dockerfile, WinSLWebApp-Dockerfile
? Select all services that are needed:
ID: move2kube.services.[].enable
Hints:
[The services unselected here will be ignored.]
 myproject
INFO[0005] Starting Plan Transformation
INFO[0005] Iteration 1
INFO[0005] Iteration 2 - 1 artifacts to process
INFO[0005] Transformer ClusterSelector processing 1 artifacts
? Choose the cluster type:
ID: move2kube.target.clustertype
Hints:
[Choose the cluster type you would like to target]
 Kubernetes
INFO[0006] Transformer ClusterSelector Done
INFO[0006] Transformer KubernetesVersionChanger processing 1 artifacts
INFO[0006] Transformer KubernetesVersionChanger Done
INFO[0006] Created 1 pathMappings and 1 artifacts. Total Path Mappings : 1. Total Artifacts : 1.
INFO[0006] Iteration 3 - 1 artifacts to process
INFO[0006] Transformer Parameterizer processing 1 artifacts
INFO[0006] Transformer Parameterizer Done
INFO[0006] Plan Transformation done
INFO[0006] Transformed target artifacts can be found at [/home/user/myproject].
```

It will take a few minutes for it to finish processing and generate a directory called `myproject`. The name of the output directory is the same as the project name (by default `myproject`). The project name can be changed using the `-n` flag.

```console
  $ ls
  kubernetes-to-kubernetes  m2k.plan  m2kconfig.yaml  m2kqacache.yaml  myproject

  $ ls myproject/
  source
```

The applications can now be deployed to Kubernetes using these generated artifacts.

## Exploring the output

The full structure of the output directory can be seen by executing the `tree` command.
```console
  $ cd myproject/
  $ tree
  .
   └── source
      ├── kubernetes-to-kubernetes-versionchanged
      │   ├── api-deployment.yaml
      │   ├── api-service.yaml
      │   ├── redis-deployment.yaml
      │   ├── redis-service.yaml
      │   ├── web-deployment.yaml
      │   ├── web-ingress.yaml
      │   └── web-service.yaml
      └── kubernetes-to-kubernetes-versionchanged-parameterized
          ├── helm-chart
          │   └── myproject
          │       ├── Chart.yaml
          │       ├── templates
          │       │   ├── api-deployment.yaml
          │       │   ├── api-service.yaml
          │       │   ├── redis-deployment.yaml
          │       │   ├── redis-service.yaml
          │       │   ├── web-deployment.yaml
          │       │   ├── web-ingress.yaml
          │       │   └── web-service.yaml
          │       ├── values-dev.yaml
          │       ├── values-prod.yaml
          │       └── values-staging.yaml
          ├── kustomize
          │   ├── base
          │   │   ├── api-deployment.yaml
          │   │   ├── api-service.yaml
          │   │   ├── kustomization.yaml
          │   │   ├── redis-deployment.yaml
          │   │   ├── redis-service.yaml
          │   │   ├── web-deployment.yaml
          │   │   ├── web-ingress.yaml
          │   │   └── web-service.yaml
          │   └── overlays
          │       ├── dev
          │       │   ├── apps-v1-deployment-api.yaml
          │       │   ├── apps-v1-deployment-redis.yaml
          │       │   ├── apps-v1-deployment-web.yaml
          │       │   └── kustomization.yaml
          │       ├── prod
          │       │   ├── apps-v1-deployment-api.yaml
          │       │   ├── apps-v1-deployment-redis.yaml
          │       │   ├── apps-v1-deployment-web.yaml
          │       │   └── kustomization.yaml
          │       └── staging
          │           ├── apps-v1-deployment-api.yaml
          │           ├── apps-v1-deployment-redis.yaml
          │           ├── apps-v1-deployment-web.yaml
          │           └── kustomization.yaml
          └── openshift-template
              ├── parameters-dev.yaml
              ├── parameters-prod.yaml
              ├── parameters-staging.yaml
              └── template.yaml
  13 directories, 42 files
```

Some things to note:
* The Helm chart in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/helm-chart` directory.
* The Kustomize YAMLs in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/kustomize` directory.
* The Openshift Template in the `source/kubernetes-to-kubernetes-versionchanged-parameterized/openshift-template` directory.
* In each case, there are three environments `dev`, `staging` and `prod`.
* It is possible to have different parameterizations for each environment.

Notice that the directory name has `versionchanged` in it. This is because two transformers are currently in play here:
* The `KubernetesVersionChanger` transformer, which was asking for the Kubernetes version to target, and which created the version changed YAMLs to suit the target cluster supported kinds and versions.
* The `Parameterizer` transformer, which was taking the version changed YAMLs and creating Helm Charts, Kustomize overlays, and OC templates.

If the intention was to retain the kind and verisons of Kubernetes YAMLs, disable the `KubernetesVersionChanger` transformer either in the QA, configuration, or in the plan, and then the YAMLs will be parameterized as is.

For more details on how to customize the parameterization that Move2Kube does look at the [parameterization documentation](/transformers/purpose-built/parameterizer).

## Conclusion

Given Kubernetes YAMLs, Move2Kube helps parameterize them and generate Helm charts, Kustomize, Openshift Templates, etc.
Move2Kube is also capable of changing the versions of various Kubernetes resources to match the target cluster.

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Move2Kube/Tutorials/createHelmChartsKustomize.md)