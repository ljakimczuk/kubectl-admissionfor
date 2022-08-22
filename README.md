# The Kubectl NGINX-based Dynamic Admission Controller for requests logging

The `kubectl-admissionfor` plugin spins up the NGINX-based Pod in within the cluster, and configures Kubernetes to sends requests to it whenever object of `<kind>` Kind and of `<group>/<version>` version is created.

In a default configuration the NGINX allows all requests and only logs the body send by the API server. However, it
can be tweaked and turn into a demo admission controller that also denies requests based on some conditions. Find
the commented out examples of that in the plugin.

## Getting Started

These instructions will get you a copy of this plugin up and running.

### Installing

To install the plugin, execute the following command:

```
sudo curl -L https://raw.githubusercontent.com/ljakimczuk/kubectl-admissionfor/main/bin/kubectl-admissionfor -o /usr/local/bin/kubectl-admissionfor
sudo chmod a+x /usr/local/bin/kubectl-admissionfor
```

### Usage

Pass the kind, group and version of the Kubernetes objects you want to scan when executing the plugin, see the help
output below.

```
Usage:
  admissionfor -k <kind> -v <version> [-g <group>]

Examples:
  # Creates fake admission controller for v1/Pods.
  kubectl admissionfor -k pods -v v1

  # Creates fake admission controller for v1/ConfigMaps.
  kubectl admissionfor -k configmaps -v v1
```

#### Scanning Pods admission

In order to scan Pods being submitted to Kubernetes run:

```sh
$ kubectl admissionfor -k pods -v1
Run the

kubectl logs -n kube-system admission-fake-a7bbcc -f

to get the objects being submitted.
```

While the plugin is running, execute the `kubernetes logs -n kube-system admission-fake-a7bbcc` to read the
admissions requests in JSON format.
