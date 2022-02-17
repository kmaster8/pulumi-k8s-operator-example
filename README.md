# Pulumi GitOps Example

[OpenGitOps Compliant](https://opengitops.dev/#principles) Pulumi Kubernetes Operator Example

# Prereqs

You need

* Pulumi Account
* Pulumi Token
* K8S Cluster

For the K8S cluster...

```shell
kind create cluster --image=kindest/node:v1.23.3
```

# Install Operator

You need to install the Pulumi Operator. You can do it with Pulumi or via YAML from [their repo](https://github.com/pulumi/pulumi-kubernetes-operator)

```shell
rm -rf pulumi-kubernetes-operator
git clone https://github.com/pulumi/pulumi-kubernetes-operator
kubectl apply -f pulumi-kubernetes-operator/deploy/crds
kubectl apply -f pulumi-kubernetes-operator/deploy/yaml
kubectl rollout status deploy/pulumi-kubernetes-operator
```

# Create Stack

Create a stack object in a YAML

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: pulumi-api-secret
type: Opaque
stringData:
  accessToken: "<PUT TOKEN HERE>"
---
apiVersion: pulumi.com/v1
kind: Stack
metadata:
  name: nginx-k8s-stack
spec:
  envRefs:
    PULUMI_ACCESS_TOKEN:
      type: Secret
      secret:
        name: pulumi-api-secret
        key: accessToken
  stack: christianh814/quickstart/dev
  projectRepo: https://github.com/christianh814/pulumi-k8s-operator-example
  branch: "refs/heads/main"
  destroyOnFinalize: true
  retryOnUpdateConflict: true
  continueResyncOnCommitMatch: true
  resyncFrequencySeconds: 60
  refresh: true
...
```

Apply

```shell
kubectl apply -f mydevstack.yaml
```
