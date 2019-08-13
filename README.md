## PodSet Operator 

Objective of this Operator/Controller is demonstrate `ReplicaSet` kind of resource
implementation using Kubernetes controller pattern.
Another objective of this repo. is to show how to build the controller from scratch and what challenges/gotchas developer could face. 
So a developer could understand the beauty [KuberBuilder](https://github.com/kubernetes-sigs/kubebuilder) or [Operator SDK](https://github.com/operator-framework/operator-sdk) frameworks


#### PodSet resource

Once user applies the `PodSet` (`kubectl apply -f podset.yaml`) resource, controller could spin up
number of pods mentioned as `replicas` filed.

e.g. User want to spin up 3 pod

```yaml
apiVersion: demo.k8s.io/v1alpha1
kind: PodSet
metadata:
  name: three-podset
spec:
  replicas: 3
```


### Step-1:
setup 

```
git clone git@github.com:hrishin/podset-operator.git hrishin/podset-operator
```

Need an API to interact with `PodSet` resource in order to create, update, delete and **watch** the resource state

### Step 2:
* create `pods` namespace
```
$ kubectl create namespace pods
```

* Create CRD to define PodSet as custom resource
```
$ kubectl apply -f resources/crd.yaml -n pods 
```

* In order create clientsets(API'S) for PodSet using go client, use [k8s.io/code-generator](https://github.com/kubernetes/code-generator)
```
$ go get k8s.io/code-generator


$ ~/go/src/k8s.io/code-generator/generate-groups.sh "deepcopy,client" \
github.com/hrishin/podset-operator/pkg/client \
github.com/hrishin/podset-operator/pkg/apis \
demo:v1alpha1
```


* Build an application
```
 $ go build -o podset .
```

* Run the application
```
$ ./podset kubeconfig=~/.kube/config
```

* Create PodSet resource
```
$ kubectl apply -f resources/cr.yaml -n pods 
```