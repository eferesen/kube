# Setup K8s

1. Install or make sure you have google cloud sdk and helm
2. Create Project via GCP Console
3. Create a cluster (command line or via GCP Console)
4. Login into the console with cluster auth string

   ```
   # Login
   gcloud container clusters get-credentials standard-cluster-1 --zone us-central1-a --project ccp-dev-254821

   ```

5. Install Tiller

```
k create serviceaccount --namespace kube-system tiller
k create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
# Wait a few minutes till Tiller is done
helm version
# Make Sure there is no error

```

6. Reserve a static IP address

Login into GCP console and reserve an static IP address
It is under VPC Network => External IP Addressess
You will need this static IP in the next step

7. Install Ingress Controller

```
# Check Context
k config current-context

# Install nginx-ingress
helm upgrade --install nginx stable/nginx-ingress --values nginx-ingress-values.yml --set controller.service.loadBalancerIP=<IP set in the previous step>

# To get rid of nginx controller
```

helm list

# get the name

helm delete <name>

```

# Check to see if the install is complete

k get all

```

8. Install Cert Manager

```
helm upgrade --install cert-manager --namespace kube-system stable/cert-manager --values ./container-engine/cert-manager-values.yml --debug

```

9. Install Letsecrypt Cluster Issuer

```
k apply -f ./container-engine/letsencrypt-clusterissuer.yaml
```
