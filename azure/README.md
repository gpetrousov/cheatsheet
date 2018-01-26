# Azure cheatsheet

### List groups
	az group list

### Delete a resource group
	az group delete --name <group name>
	az group delete --resource-group <RG>

### Show my subscription ID
	az account list -o table
	az account list | python -c "import sys, json; print(json.load(sys.stdin)[0]['id'])"

### Show locations for which azure acs (Azure Container Serice) is available
	az acs list-locations

### Deploy kube8s cluster
	 acs-engine deploy --resource-group <RG> --subscription-id `az account list | python -c "import sys, json; print(json.load(sys.stdin)[0]['id'])"` --location <location> auto-suffix --api-model kubernetes.json

### Get k8s cluster information (using the genrerated tempalte)
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' cluster-info

### Run commands in a container (the usual docker commands)
	kubectl exec

### Get running nodes on cluster
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' get nodes

### Show running pods
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' get pods --all-namespaces

### Start a docker container
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' run nginx --image nginx

### Expose a container's service port 80 (this only exposes to local network and not the internet)
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' expose deployment nginx --port=80

### Expose the service to the world (this is done by changing the service type from ClusterIP to LoadBalancer)
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' edit svc/nginx

### Make sure that the type has changed
	watch -n 1 kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' get svc

### Open interactive terminal inside docker container
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' exec <containerID> -it bash

### Enable web interface for kube8s
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' proxy
	GOTO http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all

### List your deployments	
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.<location>.json' get deployments

### Create a service using the definition in example-service.yaml
	kubectl create -f example-service.yaml

### Stop/Delete a service
	kubectl --kubeconfig='kubeconfig.<location>.json'  delete service <service_name>