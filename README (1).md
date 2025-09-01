# K8- – WordPress + MariaDB on Kubernetes

Deploy a simple WordPress stack on Kubernetes with a **MariaDB StatefulSet**, **DB Service**, **WordPress Deployment/Service**, and an **Ingress** for external access.

## Repository layout
- `stateful.yaml` – MariaDB `StatefulSet` + PVCs  
- `dbservice.yaml` – ClusterIP `Service` exposing the DB to WordPress  
- `wordpress.yml` – WordPress `Deployment` + `Service`  
- `ingress.yml` – Ingress rule to expose WordPress via hostname

## Prerequisites
- Kubernetes cluster (Minikube, k3s, EKS, etc.)
- `kubectl` configured
- A default `StorageClass` (for MariaDB PVCs)
- Ingress controller (e.g., ingress-nginx) if you plan to use `ingress.yml`

## Quick start
> Adjust namespaces/hostnames inside the YAMLs if needed before applying.

```bash
# 1) Optional: create a namespace
kubectl create namespace web || true

# 2) Apply manifests (order matters)
kubectl apply -n web -f stateful.yaml
kubectl apply -n web -f dbservice.yaml
kubectl apply -n web -f wordpress.yml
kubectl apply -n web -f ingress.yml

# 3) Verify
kubectl get pods,svc,ingress -n web
```

### Accessing WordPress
- **With Ingress**: point your DNS or `/etc/hosts` to the host in `ingress.yml`, then browse `http://<host>`.
- **Without Ingress**: port-forward the WordPress service:
  ```bash
  kubectl -n web port-forward svc/wordpress 8080:80
  # open http://localhost:8080
  ```

## Configuration notes
- **DB credentials**: ensure `wordpress.yml` and `stateful.yaml` use matching `MYSQL_*` env vars/Secrets.
- **Persistence**: MariaDB uses PVCs from the cluster’s default `StorageClass`. Change sizes or classes in `stateful.yaml`.
- **Images**: update the container image references in `wordpress.yml` as needed (e.g., your ECR image).
- **Namespace**: if you don’t want `web`, remove `-n web` and/or change metadata.namespace in the YAMLs.

## Troubleshooting
- **`Pending` PVC**: define or fix default `StorageClass`.
- **ImagePullBackOff**: make sure your registry secret (e.g., `regcred`) exists in the same namespace:
  ```bash
  kubectl -n web get secret regcred
  ```
- **Ingress 404**: confirm the ingress controller is installed and the host in `ingress.yml` resolves to the controller’s IP.

## Cleanup
```bash
kubectl delete -n web -f ingress.yml -f wordpress.yml -f dbservice.yaml -f stateful.yaml
kubectl delete namespace web
```
