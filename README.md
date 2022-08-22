# The Kubectl NGINX-based Dynamic Admission Controller for requests logging

The `kubectl-admissionfor` plugin spins up the NGINX-based Pod in within the cluster, and configures Kubernetes to sends requests to it whenever object of `<kind>` Kind and of `<group>/<version>` version is created.

In a default configuration the NGINX allows all requests and only logs the body send by the API server. However, it
can be tweaked and turn into a demo admission controller that also denies requests based on some conditions. Find
the commented out examples of that in the plugin's ConfigMap.

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

In order to scan Pods being submitted to Kubernetes, run:

```sh
$ kubectl admissionfor -k pods -v v1
Run the

kubectl logs -n kube-system admission-fake-a7bbcc -f

to get the objects being submitted.
```

While the plugin is running, execute the `kubernetes logs -n kube-system admission-fake-a7bbcc -f` to read the
admissions requests in a JSON format.

From there you can easily use `jq` to query the information you need.

#### Running a demo validating

The plugin's ConfigMap for NGINX contains lines that are commented out, and that when enabled runs validation on
Pods objects being created:

```text
-- Example of checking the name meets the expectation towards it
--
--[[
if not string.match(req_body.request.object.metadata.name,'nginx.*') then
    rev_allow = 'false'
    rev_code = 403
    rev_msg = 'No other name than \\\"nginx\\\" can be used.'
end
--]]

-- Example of checking each container meets expectations towards image used
--
--[[
for key,value in pairs(req_body.request.object.spec.containers) do
    if value.image ~= 'nginx:1.14.2' then
        rev_allow = 'false'
        rev_code = 403
        rev_msg = 'No other image than \\\"nginx:1.14.2\\\" can be used.'
    end
end
--]]
```

Uncomment these lines or add your own for the resources you need in order to turn this plugin into a demo, yet legit validating admission controller.
