# Azure cheatsheet

##### List groups
	az group list

##### Delete a resource group
	az group delete --name <group name>
	az group delete --resource-group <group name>

##### Show my subscription ID
	az account list -o table
	az account list | python -c "import sys, json; print(json.load(sys.stdin)[0]['id'])"

##### Create postgresql PaaS
	az postgres server create --resource-group <group name> --name <DB name>  --location westeurope --admin-user <DB uname> --admin-password `openssl rand -base64 24` --performance-tier Basic --compute-units 50 --version 9.6


### Azure Container Service

##### Show locations for which azure acs (Azure Container Serice) is available
	az acs list-locations

##### Create azure container service (ACS) with multiple agent pools (future azure extension Jan 8 2018 not supported yet)
	https://github.com/Azure/ACS/blob/master/announcements/2017-08-22_scenario_usage.md
	az acs create -n k8sTest -g k8sTest  -t kubernetes -a "[{'name':'agentpool1'},{'name':'agentpool2','vmSize':'Standard_D2','count':1}]"

##### Deploy kube8s cluster (using acs-engine)
	 acs-engine deploy --resource-group <RG> --subscription-id <subID> --location <location> auto-suffix --api-model kubernetes.json

##### Create a private azure registry on Standard SKU
	az acr create --resource-group <group name> --name <registry name> --sku <SKU types>

##### Login to private registry named
	az acr login --name <registry name>

##### Example to pull, tag and push to private repository
	docker pull microsoft/aci-helloworld
	az acr list --resource-group <group name> --query "[].{acrLoginServer:loginServer}" --output table
	docker tag microsoft/aci-helloworld <registry name>.azurecr.io/aci-helloworld:v1
	docker push <registry name>.azurecr.io/aci-helloworld:v1

##### List the repositories/images in a registry:
	az acr repository list --name <registry name> --output table
	az acr repository show-tags --name <registry name> --repository image_name --output table

##### List container images
	az acr repository list --name <acrName> --output table


### Backup jobs

##### Show all backup policies
	az backup job list --resource-group <group name> --vault-name <vault name>
