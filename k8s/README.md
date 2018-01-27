# Kubernetes cheatsheet

##### Get k8s cluster information (using the genrerated tempalte)
	kubectl cluster-info

##### Run commands in a container
	kubectl exec <container name> <command>

##### Get running nodes on cluster
	kubectl get nodes

##### List your deployments	
	kubectl get deployments

##### Show running pods
	kubectl get pods --all-namespaces

##### List your services	
	kubectl get svc

##### Start a docker container using image from cloud
	kubectl run nginx --image nginx

##### Start a docker container using template file
	kubectl create -f <template.yml> --record

##### Perform dry run on a deployments file (useful for syntax check)
	kubectl create -f <template.yml> --dry-run

##### Expose a container's service port 80
	kubectl expose deployment nginx --port=80

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

##### Delete/Reinstall Tiller
	kubectl delete deployment tiller-deploy --namespace kube-system

##### Portforwarding from local to pod (awesome). This will forward a service running on port 5432 to localhost on port 5432. You can run commands against localhost now on the same port
	kubectl port-forward $POD_NAME 5432:5432

##### Delete all errored pods
	kubectl get pod --show-all | grep Error | awk '{print $1}' | xargs kubectl delete pod

##### Update deployment
	kubectl apply -f definitions.yml