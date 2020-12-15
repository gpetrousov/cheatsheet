# Kubernetes cheatsheet

##### Get k8s cluster information (using the genrerated tempalte)
	kubectl cluster-info

##### Run commands in a container
	kubectl exec <container name> <command>

##### Get running nodes on cluster
	kubectl get nodes

-------------------

### Pods

##### Pod definition example
```
apiVersion: v1
kind: Pod
metadata:
  name: static-web
  labels:
    role: myrole
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - name: web
          containerPort: 80
          protocol: TCP
```

##### Referencing pod labels
- when referencing pods based on label, the selection does not need to have all the labels
- if more pods have the same label, you can specificy more labels to restrict your selection

##### Show running pods from all namespaces
```
kubectl get pods --all-namespaces
```

##### Delete a pod
```
	kubectl delete pods <pod-name>
```

##### Filter pods by label
```
kubectl get pods --selector role=myrole
```

##### Portforwarding from local to pod (awesome). This will forward a service running on port 5432 to localhost on port 5432. You can run commands against localhost now on the same port
```
	kubectl port-forward $POD_NAME 5432:5432
```

##### Delete all errored pods
```
	kubectl get pod --show-all | grep Error | awk '{print $1}' | xargs kubectl delete pod
```

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

##### Show all items from all namespaces
```
kubectl get all --all-namespaces
```


---------------------
## Passing values to pods

### ConfigMaps

- Just like env variables

##### Create ConfigMap from CLI
```
	kubectl create cm <cm-name> --from-literal=VAR1=value1\
	VAR2=value2
```

### Secrets

- For sensitive data (passwords)
- Still visible when requested from API calls
- Better use Helm Secrets or Hashicorp Vault

##### Create Secret from CLI
```
	kubectl create secret generic \
	<secret-name> \
	--from-literal=DB_PASSWD=verysecretpassword \
	--from-literal=APP_PASSWD=anotherverysecretvalue
```

##### Create Secret from file
```
	kubectl create secret generic <secret-name> --from-file=<path-to-file>
```

---------------------

### Searching in the documentation
##### Searching the docs from cli
```
	kubectl explain pods
	kubectl explain pods --recursive | grep envForm -A3
```
- `--recursive`: Add the --recursive flag to display all of the fields at once without descriptions. Information about each field is
retrieved from the server in OpenAPI format.
- `-A3`: Print first 3 lines

---------------------

### Taints & Tolerations

- `Taints`: Instruct node(s) to deny pods without a toleration
- `Tolerations`: Apply a toleration on a pod to satisfy a `Taint` on a node
- Taints and Tolerations don't specify to pods specific nodes to run on (see node affinity)

`taint-effects`:
- NoSchedule
- PreferNoSchedule
- NoExecute

- Tolerations are applied in the pod definition

##### Apply `Taint` on a node
```
kubectl taint nodes <node-name> key=value:<taint-effect>
i.e.
Kubectl taint nodes node2 app=blue:NoSchedule
```

##### Remove`Taint` from a node
```
kubectl taint nodes <node-name> key=value:<taint-effect>-
i.e.
Kubectl taint nodes node2 app=blue:NoSchedule-
```
---------------------

### Node selectors

 - ideal for simple scheduling of pods on nodes
 - does not support logical operations
 - schedluing is based on node labels

##### Add label on a node
 ```
 kubect label node node03 color=blue
 ```

---------------------

### Node Affinity

- allows pod scheduling on specific nodes
- rules support logical operations
- rules are based on labels

---------------------

### Multi-container pods

- run multiple containeres in a pod
- three design patterns
 `sidecar`, `ambassador`, `adapter`
- just add container information in the `containers` tag


---------------------

### Liveness & Readiness

- `Liveness`: restarts the container which does not statisfy the probe
- `Readiness`: delays traffic until container is ready to accept traffic
- probe types: http, socket, command

Example

```
livenessProbe/readinessProbe:
  httpGet:
    path: /status
	port: 443
  initialDelaySeconds: 10
  periodSeconds: 5
  failureThreshold: 3
```

---------------------

### Reading logs from containers

##### Read logs continuously

```
kubectl logs -f <pod-name>
# Or in case of >1 containers in same pod
kubectl logs -f <pod-name> <container-name>
```


---------------------
### Rolling updates and Rollback in deployments

- each deployment creates a new rollout
- a rollout is the process of container deployments on the cluster
- by default k8s uses `RollingUpdates` rollout - for each new container destroy the previous one
- you can record a change in teh deployment using `--record` flag when editing

##### Get rollout status of a deoployment
```
kubectl rollout status deployment/<my-deployment-name>
```

##### Get history of rollouts
```
kubectl rollout history deployment/<my-deployment-name>
```

##### Undo a deployment
```
kubectl rollout undo deployment/<my-deployment-name>
```

---------------------

### Jobs and CronJobs

- jobs execute a task and exit
- jobs are running inside pods

##### Job definition example

```

```

---------------------

### Services

- services enable connectivity to the pods
- service types: NodePort, ClusterIp, LoadBalancer
		- `NodePort`: allocate an ephemeral port on the node to access the pod
		- `LoadBalancer`: create a loadbalancer utilising the underlying cloud provider
		- `ClusterIP` (default type): allow access to multiple pods on multiple clusters via a single endpoint
- services are linked to pods using selectors
- naming:
		- TargetPort: port on the pod (80) - where pod is exposed
		- Port: port on the service (80) - where service is exposed
		- NodePort: ephemeral port on the node (30008)
- service definition example
```
apiVersion: v1
kind: service
metatada:
  name: my-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
	  nodePort: 30008
  selectors:
    app: myapp
	type: front-end
```

##### List your services
```
kubectl get svc
```

---------------------

### Ingress



---------------------

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

##### Update deployment
	kubectl apply -f definitions.yml

---------------------

### Assistive options

##### Test if syntax is correct and resource can be created
```
--dry-run=client
```

##### Get all objects matching a label
```
kubectl get all --selector env=prod
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
 - https://github.com/lucassha/CKAD-resources
 - https://github.com/dgkanatsios/CKAD-exercises

