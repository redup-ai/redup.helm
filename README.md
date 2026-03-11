# redup.helm

![Helm Test status](https://github.com/redup-ai/redup.helm/actions/workflows/helm-test.yml/badge.svg?branch=master)


# Usage

This repository provides a template for creating a Helm chart for any application. The repository assumes that we have an image with the application in some registry.

**Note:** This template is intended for deployment in a Kubernetes cluster based on [Deckhouse](https://github.com/deckhouse/deckhouse) (e.g. HPA with PodMetric, pod-reloader annotations, and other Deckhouse-specific options are used by default).

## Add as a submodule
```
git submodule add -f -b master https://github.com/redup-ai/redup.helm.git .helm
```

### Keep data in sync with master

We always assume backward compatibility of the master branch.

```
# .gitmodules
[submodule ".helm"]
    path = .helm
    url = https://github.com/redup-ai/redup.helm.git
    branch = master
```

## Initialize the values yaml file

Any fields from [values.yaml](values.yaml) can be overridden here. If a field is not specified, it is not changed and the default value is used.

The following example makes these changes:
1. Changes the application port to `8080` and marks it as an HTTP application.
2. Removes the StatefulSet definition (this is not recommended, it is just an example).

```
touch values.yaml

# values.yaml
service:
  servicePorts:
  - port: 8080
    name: http-app
    protocol: TCP
    
deployment:
  livenessProbe:
    tcpSocket:
      port: 8080
  readinessProbe:
    tcpSocket:
      port: 8080
  startupProbe:
    tcpSocket:
      port: 8080
  
  containerPorts:
  - containerPort: 8080
    name: http-app
    protocol: TCP

statefulset: null
```

## Initialize the package yaml file

Here we define all settings of the Helm project.

```
# package.yaml
name: my.awesome.application.package # It is recommended to keep this similar to appName (for example `my.awesome.application`), but it is not required.
version: 1.21.10 # Package (Chart) version. It can be similar to the image version, but may change independently when the Helm chart is updated.
appName: my.awesome.application
appVersion: 0.24.1 # Application (Docker image) version
```
