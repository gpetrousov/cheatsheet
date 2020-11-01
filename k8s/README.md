# Kubernetes cheatsheet

##### Get k8s cluster information (using the genrerated tempalte)
	kubectl cluster-info

##### Run commands in a container
	kubectl exec <container name> <command>

##### Get running nodes on cluster
	kubectl get nodes

-------------------

### Pods

##### Show running pods
	kubectl get pods --all-namespaces

##### Delete a pod
```
	kubectl delete pods <pod-name>
```

##### Portforwarding from local to pod (awesome). This will forward a service running on port 5432 to localhost on port 5432. You can run commands against localhost now on the same port
	kubectl port-forward $POD_NAME 5432:5432

##### Delete all errored pods
	kubectl get pod --show-all | grep Error | awk '{print $1}' | xargs kubectl delete pod

-------------------

### ReplicaSets
Multiple pods are deployed using ReplicaSets.
ReplicaSets control the number of pod instances running on kubernetes.

- ReplicaSets automatically create pods

##### Creeate a ReplicaSet
    ```
    kubectl create -f replicaset_definition.yml
	```

##### Get ReplicaSets
    ```
	kubect get replicaset
    ```

##### Delete ReplicaSets
    ```
	kubect delete replicaset <name-of-replicaset>
    ```

##### Replace ReplicaSets
    ```
	kubect replace replicaset -f <name-of-file>
    ```

##### Scale ReplicaSets
    ```
	kubect scale replicasets --replicas=4 -f <name-of-file>
	kubect scale replicasets --replicas=5 <name-of-replicaset>
    ```

---------------------

### Deployments
Provides mechanists to update the underlying instances seamlessly, such as RollingUpdates, UndoPods, etc. 
- Deployments create replicasets which create pods

##### Create a deployment
```
	kubectl create -f <template.yml>
```

##### List your deployments	
```
	kubectl get deployments
```

##### Perform dry run on a deployments file (useful for syntax check)
```
	kubectl create -f <template.yml> --dry-run
```

##### Expose a container's service port 80
```
	kubectl expose deployment nginx --port=80
```

##### Delete/Reinstall Tiller
```
	kubectl delete deployment tiller-deploy --namespace kube-system
```

---------------------

### Namespaces

- are like terraform workspaces
- default namespace is called "default"
- pods in the same namespace can connect directly to eachother
- pods in different namespaces can connect to eachother using the format

```
<service_name>.<namespace>.<service>.cluster.local
|------------| |---------| |-------| |-----------|
i.e.
- my-db-svc.dbtier.svc.cluster.local
- my-cache-svc.uat.svc.cluster.local
```

##### Show running pods in all namespaces
```
	kubectl get pods --all-namespaces
```


---------------------


##### List your services	
	kubectl get svc

##### Start a docker container using image from cloud
	kubectl run nginx --image nginx

##### Start a docker container using template file
	kubectl create -f <template.yml> --record

##### Expose the service to the world (this is done by changing the service type from ClusterIP to LoadBalancer)
	kubectl edit svc/nginx

##### Make sure that the type has changed
	watch -n 1 kubectl get svc

##### Open interactive terminal inside docker container (just with docker)
	kubectl  exec <container name> -it bash

##### Enable web interface for kube8s
	kubectl  proxy
	GOTO http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all

##### Create a service using the definition in example-service.yaml
	kubectl create -f example-service.yaml

##### Delete a service
	kubectl delete service kubernetes
##### Update deployment
	kubectl apply -f definitions.yml

---------------------

### Assistive options

##### Test if syntax is correct and resource can be created
```
--dry-run=client
```

##### Output YAML definition on tthe screen
```
-o yaml
```

Example

```
kubectl create deployment my-redis --image redis:alpine --namespace dev --dry-run=client -o yaml > my_deployment.yaml
```


## References

 - https://kubernetes.io/docs/reference/kubectl/cheatsheet

