# Azure cheatsheet

### List groups
	az group list

### Delete a resource group
	az group delete --name <group name>
	az group delete --resource-group salesforcek8s

### Show my subscription ID
	az account list -o table
	az account list | python -c "import sys, json; print(json.load(sys.stdin)[0]['id'])"

### Show locations for which azure acs (Azure Container Serice) is available
	az acs list-locations

### Create azure container service (ACS) with multiple agent pools (future azure extension Jan 8 2018 not supported yet)
	https://github.com/Azure/ACS/blob/master/announcements/2017-08-22_scenario_usage.md
	az acs create -n k8sTest -g k8sTest  -t kubernetes -a "[{'name':'agentpool1'},{'name':'agentpool2','vmSize':'Standard_D2','count':1}]"

### Deploy kube8s cluster
	 acs-engine deploy --resource-group <RG> --subscription-id <subID> --location <location> auto-suffix --api-model kubernetes.json

### Get k8s cluster information (using the genrerated tempalte)
	kubectl --kubeconfig='_output/<RG>/kubeconfig/kubeconfig.westeurope.json' cluster-info

### Run commands in a container
	kubectl exec

### Get running nodes on cluster
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' get nodes

### Show running pods
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' get pods --all-namespaces

### Start a docker container (Create a k8s object)
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' run nginx --image nginx
	kubectl create -f html2Pdf.yml --record

### Perform dry run given a deployments file (useful for syntax check)
	kubectl create -f html2Pdf.yml --dry-run

### Expose a container's service port 80 (this only exposes to local network and not the internet)
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' expose deployment nginx --port=80

### Expose the service to the world (this is done by changing the service type from ClusterIP to LoadBalancer)
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' edit svc/nginx

### Make sure that the type has changed
	watch -n 1 kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' get svc

### Open interactive terminal inside docker container
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' exec nginx-4217019353-p0dsm -it bash

### Enable web interface for kube8s
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' proxy
	GOTO http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all

### List your deployments	
	kubectl --kubeconfig='_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json' get deployments

### Create a private azure registry on Standard SKU
	az acr create --resource-group salesforcek8s --name salesforceregistry --sku Managed_Standard

### Login to private registry named backendregistry
	az acr login --name backendregistry

### Example to pull, tag and push to private repository
	docker pull microsoft/aci-helloworld
	az acr list --resource-group salesforcek8s --query "[].{acrLoginServer:loginServer}" --output table
	docker tag microsoft/aci-helloworld backendregistry.azurecr.io/aci-helloworld:v1
	docker push backendregistry.azurecr.io/aci-helloworld:v1

### List the repositories/images in a registry:
	az acr repository list --name backendregistry --output table
	az acr repository show-tags --name backendregistry --repository image_name --output table


### Create a service using the definition in example-service.yaml
	kubectl create -f example-service.yaml

### Stop/Delete a service (But it will be recreated anyway)
	kubectl --kubeconfig='../salesforce/_output/salesforcek8s/kubeconfig/kubeconfig.westeurope.json'  delete service kubernetes

### Delete/Reinstall Tiller
	kubectl delete deployment tiller-deploy --namespace kube-system

### portforwarding from local to pod (Fucking awesome);This will forward a service running on port 5432 to localhost on port 5432;You can run commands against localhost now on the same port
	kubectl port-forward $POD_NAME 5432:5432

### Create postgresql PaaS
	az postgres server create --resource-group backendK8s --name backendpgsql  --location westeurope --admin-user bronroot --admin-password `openssl rand -base64 24` --performance-tier Basic --compute-units 50 --version 9.6

### Kubernetes cluster information
	kubectl cluster-info

### list all the events associated with the pod, including pulling of images, starting of containers.
	kubectl describe pods

### List container images
	az acr repository list --name <acrName> --output table

### Delete all errored pods
	kubectl get pod --show-all | grep Error | awk '{print $1}' | xargs kubectl delete pod

### Update deployment
	kubectl apply -f definitions.yml


## Backups ##

### Show all backup policies
	az backup job list -g VandeBron-IT -v VAULT-JENKINS

### Extract details about jobs in backup
	# job name
	az backup job list -g VandeBron-IT -v VAULT-JENKINS | python -c "import sys, json; print(json.load(sys.stdin)[0]['name'])"	
	az backup job list -g VandeBron-IT -v VAULT-JENKINS | python -c "import sys, json; print(json.load(sys.stdin)[0]['resourceGroup'])"

### Show details of a particular job
	az backup job show --name aa09ec49-1944-42b5-aa4b-bce668c31b10 --resource-group VandeBron-IT --vault-name VAULT-JENKINS
	
### Show backup jobs for specified vault
	az backup job list -g VandeBron-IT -v VAULT-JENKINS
	az backup job list -g VandeBron-IT -v VAULT-JENKINS | python3 -c "import sys, json, pprint; pprint.pprint(json.load(sys.stdin)[0])"
	az backup job list -g VandeBron-IT -v VAULT-JENKINS | python3 -c "import sys, json, pprint; pprint.pprint(json.load(sys.stdin)[0].keys())"
	az backup job list -g VandeBron-IT -v VAULT-JENKINS | python3 -c "import sys, json, pprint; pprint.pprint(json.load(sys.stdin)[0]['properties']['status'])"