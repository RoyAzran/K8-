# K8- Project

This repo holds the YAML files I used to deploy WordPress and MariaDB on Kubernetes with Grafana monitoring.

## Files
- `stateful.yaml` – MariaDB StatefulSet  
- `dbservice.yaml` – DB service  
- `wordpress.yml` – WordPress deployment/service  
- `ingress.yml` – Ingress rules  
- `get_helm.sh` – Helm install script  
- `minikube-linux-amd64` – Minikube binary

## What I did
1. Created the manifests (`stateful.yaml`, `dbservice.yaml`, `wordpress.yml`, `ingress.yml`).  
2. Applied them on my cluster.  
3. Set up Grafana through kube-prometheus-stack (via Helm).  
4. Used port-forwarding and SSH tunneling to access Grafana and WordPress locally.

## Commands I used

### Apply YAML files
```bash
kubectl apply -f stateful.yaml
kubectl apply -f dbservice.yaml
kubectl apply -f wordpress.yml
kubectl apply -f ingress.yml
```

### Port-forward Grafana
```bash
kubectl port-forward svc/stack-grafana 8080:80
# open http://localhost:8080
```

### Port-forward WordPress
```bash
kubectl port-forward svc/wordpress 8081:80
# open http://localhost:8081
```
