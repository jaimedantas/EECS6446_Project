# Kubernetes API for Scaling Resources

Using the API provided by Kubernetes, we can interact with out cluster,
get the current status of deployments, change the replica count for each
deployment and many more. You can check out a list of kubernetes client
libraries for different programming languages in
[in their documentations](https://kubernetes.io/docs/reference/using-api/client-libraries/).
To follow along with this tutorial, you will need to install and configure
the [Python client library for kubernetes](https://github.com/kubernetes-client/python/).

To ease the process of working with jupyter notebooks on the master node,
we suggest installing Visual Studio Code outlined in the requirements section
along with the suggested extensions.

First, let's install the necessary library to interact with the
Kubernetes API:

```sh
pip install kubernetes==12.0.1
```

Then, we can move to our python code. You may use the [VS Code Jupyter Extension](https://code.visualstudio.com/docs/python/jupyter-support) for the next steps, or use simple `.py` files.
The following imports libraries
and defines a couple of helper functions that we can use later on.

```py
from kubernetes import client, config
config.load_kube_config()

api_instance = client.AppsV1Api()

def get_replica_and_ready(deployment_name, deployment_ns="default"):
    api_response = api_instance.read_namespaced_deployment(deployment_name, deployment_ns)
    return api_response.status.replicas, api_response.status.ready_replicas

def set_replica_num(rnum, deployment_name, deployment_ns="default"):
    rnum = int(rnum)
    if rnum < 1:
        rnum = 1
    api_response = api_instance.read_namespaced_deployment(deployment_name, deployment_ns)
    api_response.spec.replicas = rnum
    api_instance.patch_namespaced_deployment_scale(deployment_name, deployment_ns, api_response)
```

Now we can use these functions to get or change the replica count for each deployment. For example, to get the number of set and ready pods for the `frontend` deployment, we can use the following:

```py
get_replica_and_ready('frontend')
```

Similarly, to set the number of pods in `frontend` deployment to 3, we can use:

```py
set_replica_num(3,'frontend')
```

## Using the Kubernetes API from Your Remote Machine

If you want to interact with your cluster using your computer, you need to make sure that the Kubernetes you installed on your computer has the correct configuration to connect to your remote cluster. Kubernetes uses a `kubeconfig` file to organize information about clusters, users, namespaces, and authentication mechanisms. This file is located in the `$HOME/.kube` directory and is named `config`. 

In order to create a configuration file for your local Kuberneters, you need to follow these steps:
1. Go to your master node and copy the `config` file. You can use the  `cat` command to displaying the file's content and then copying it.
```sh
cat ~/.kube/config
```
2. On your local machine, create a file named `config` in the Kubernetes configuration directory using `vim` or `nano`.
```sh
vim ~/.kube/config
```
3. Paste the content of the `config` file you copied from the master node. 

To check if your Kubernetes is pointing to the remote cluster, run the following command.
```sh
$ kubectl get pods
NAME                                     READY     STATUS    RESTARTS   AGE
svclb-loadgenerator-rn28h                1/1       Running   0          22h
svclb-frontend-external-g5g6s            1/1       Running   0          22h
productcatalogservice-7f857c47f-5pbml    1/1       Running   0          22h
frontend-6b64dc9665-l28tx                1/1       Running   0          22h
svclb-frontend-external-g6gxx            1/1       Running   0          22h
svclb-loadgenerator-468hz                1/1       Running   0          22h
shippingservice-75f7f9dc6c-wcwqs         1/1       Running   0          22h
paymentservice-98cb47fff-rcq4t           1/1       Running   0          22h
redis-cart-74594bd569-42wwm              1/1       Running   0          22h
checkoutservice-85d4b74f95-f7487         1/1       Running   0          22h
currencyservice-6d7f8fc9fc-szsmt         1/1       Running   0          22h
emailservice-798f4f5575-z4tsn            1/1       Running   0          22h
recommendationservice-5bf5bcbbdf-dwlkt   1/1       Running   0          22h
cartservice-675b6659c8-jkxbz             1/1       Running   1          22h
adservice-5f6f7c76f5-jtlxk               1/1       Running   0          22h
loadgenerator-b76b4568d-xh457            1/1       Running   4          22h
frontend-6b64dc9665-vz5sx                1/1       Running   0          19h
frontend-6b64dc9665-lztxf                1/1       Running   0          19h
svclb-frontend-external-gj4h8            1/1       Running   0          2h
svclb-loadgenerator-82hdk                1/1       Running   0          2h
```
Please note that you still need to install the [Python client library for kubernetes](https://github.com/kubernetes-client/python/) on your local machine to use the Kubernetes API locally.

You probably won't need to use other Kubernetes APIs for this project, but in
case you were interested, you can check out [their documentation](https://github.com/kubernetes-client/python/blob/master/kubernetes/README.md).

[Next Step](08-hpa-test.md) -->

## References

- [Github: Kubernetes Python Client](https://github.com/kubernetes-client/python)
- [Kubernetes Python Client: Examples](https://github.com/kubernetes-client/python/tree/master/examples)
