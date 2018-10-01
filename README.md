# sal-kubernetes

My configs for deploying Sal server on Kubernetes

## Prerequisites

* A Kubernetes cluster, can be on-prem or any other major cloud provider
* Some ingress controller setup in your Kubernetes cluster.  My configs assume you have an nginx ingress controller
* A default storage class configured on your cluster

### Getting Started

The `deploy` folder contains all the resources you need to deploy Postgres and the Sal server on your Kubernetes cluster.

All of these configs will assume you have a namespace on your cluster called `sal`.  The resources will be deployed into that namespace.


1. Create the namespace:
```
kubectl apply -f deploy/namespace.yml
```

2. Setup your configs and secrets
    1. Fill in the `DB_NAME` key within the `configmap.yml` file.  That value should be set to the database name that your Sal instance will use.  Change the `DOCKER_SAL_TZ` key to your appropriate Time Zone.  Apply the configmap by running: `kubectl apply -f configmap.yml`
    2. Base64 encode all the values for the keys in the `secrets.yml` file.  Apply the config by running: `kubectl apply -f secrets.yml`

3. (Optional) Deploy Postgres
	1. Don't use this in production. This is only for POC purposes
	2. Within the `deploy/postgres` folder, fill in the `POSTGRES_PASSWORD` key with your base64 encoded value and apply the secret: `kubectl apply -f deploy/postgres/secret.yml`
	3. Apply the `sal-postgres-deployment.yml` file by running: `kubectl apply -f deploy/postgres/sal-postgres-deployment.yml`.  This will create resources to run a Postgres server instance in your cluster.

4. Deploy Sal Server
	1. Create the service within the cluster by running: `kubectl apply -f deploy/sal-server/service.yml`
	2. Modify the `ingress.yml` file at `deploy/sal-server`.  Change all entries of `sal.example.com` to the subdomain you plan to use for your Sal instance.  Additionally, if you have a TLS secret in your cluster, reference that secret by changing the value of the `secretName` key.  Apply the file by running: `kubectl apply -f deploy/sal-server/ingress.yml`
	3. Lastly, deploy Sal.  As of 10/1/2018, this deployment file reference Sal image tag 3.3.10.  You may change line 26 to pull any image tag of Sal that you would like to deploy.  Once ready to deploy, run: `kubectl apply -f deploy/sal-server/sal-server-deployment.yml`  
