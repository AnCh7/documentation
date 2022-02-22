# Overview

- The **filestore** is where binaries are physically stored.
- The **database** maps a file’s [checksum](https://www.jfrog.com/confluence/display/JFROG/Checksum-Based+Storage) to its physical storage, and many operations on files within repositories are implemented as transactions in the database.
- Artifactory uniquely stores artifacts using checksum-based storage.
- The `join key` is used internally for creating trust between micro services of the same service, for example between Artifactory and Xray.
- The `master key` is used by Artifactory to securely synchronize files between cluster nodes. It is responsible for encrypting and decrypting the  shared data in the database.



# SaaS

Register:
> https://jfrog.com/start-free/

URL: `https://xxxxxxx.jfrog.io/ui/login`
Username: `name@example.com`
Password: `xxxxxxx`



# Self-Hosted

Register and get license key:
> https://jfrog.com/start-free/#hosted

The default user credentials for logging into the platform are:
```
username: admin
password: password
```

Check email for a license keys:
- JFrog Artifactory License Key
- JFrog Xray License Key



### System Requirements

> https://www.jfrog.com/confluence/display/JFROG/System+Requirements



### Install Artifactory (Helm chart)

> https://github.com/jfrog/charts/tree/master/stable/artifactory
> https://github.com/jfrog/charts/tree/master/stable/artifactory-ha
> 
> https://www.jfrog.com/confluence/display/JFROG/Installing+Artifactory
> https://www.jfrog.com/confluence/display/JFROG/Helm+Charts+for+Advanced+Users

- Connect to cluster and configure Helm CLI

```bash
kubectl config use-context xxxxxxxx

# check tiller namespaces and Helm version
kubectl get pods -A --field-selector status.phase=Running -o json | grep tiller | grep image

# install helm
asdf plugin-add helm https://github.com/Antiarchitect/asdf-helm.git
asdf install helm 2.17.0
asdf shell helm 2.17.0

# validate connection
kubectl version
helm version --tiller-namespace platform-tiller

# list helm charts
# v1
helm list --kube-context ${CONTEXT} --tiller-namespace tiller
# v3
helm list --kube-context ${CONTEXT} --all --all-namespaces
```

- generate keys
```bash
# Add JFrog Helm repository
helm repo add jfrog https://charts.jfrog.io
helm repo update

# Create a unique master key
export MASTER_KEY=$(openssl rand -hex 32)
echo ${MASTER_KEY}

# Create a unique join key
export JOIN_KEY=$(openssl rand -hex 32)
echo ${JOIN_KEY}
```

- Install chart
```bash
# Clone Helm chart repo
git clone git@github.com:jfrog/charts.git && cd "$(basename "$_" .git)"/stable/artifactory

# TODO change v2 to v1

helm dependency update
cd charts
tar -xvzf postgresql-10.3.18.tgz
rm -rf postgresql-10.3.18.tgz

# TODO postgresql change v2 to v1

# Create namespace
kubectl create namespace artifactory
kubectl get namespaces

#  --tiller-namespace tiller \
# Install the chart
helm upgrade \
  --kube-context ${CONTEXT} \
  --install artifactory \
  --namespace artifactory \
  --set artifactory.masterKey=${MASTER_KEY} \
  --set artifactory.joinKey=${JOIN_KEY} \
  -f values-medium.yaml \
  .
```

- Connect to Artifactory

```bash
Release "artifactory" does not exist. Installing it now.
NAME: artifactory
LAST DEPLOYED: Wed Aug 25 10:24:43 2021
NAMESPACE: artifactory
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations. You have just deployed JFrog Artifactory!

1. Get the Artifactory URL by running these commands:

   NOTE: It may take a few minutes for the LoadBalancer IP to be available.
         You can watch the status of the service by running 'kubectl get svc --namespace artifactory -w artifactory-artifactory-nginx'

   export SERVICE_IP=$(kubectl get svc --namespace artifactory artifactory-artifactory-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   echo http://$SERVICE_IP/

2. Open Artifactory in your browser
   Default credential for Artifactory:
   user: admin
   password: password
```

- Check logs

```bash
kubectl --namespace artifactory get pods
kubectl --namespace artifactory logs -f xxxxxxxx
```



### Adding License

Using a Kubernetes secret or creating a secret as part of the Helm release

```shell
# Remove all newline characters in licenseKey value
curl --request POST \
  -u admin:xxxxxx \
  --url http://128.0.0.1/artifactory/api/system/licenses \
  --header 'Content-Type: application/json' \
  --data '{
  "licenseKey":"xxxxxx"
}'
```



### Post-Installation Steps

- Change the default admin password

```sh
curl --request POST \
  -u admin:xxxxxx \
  --url http://128.0.0.1/artifactory/api/security/users/authorization/changePassword \
  --header 'Content-Type: application/json' \
  --data '{
	  "userName": "admin", 
	  "oldPassword": "xxxxxxxxx",
	  "newPassword1": "yyyyyyyyy",
	  "newPassword2": "yyyyyyyyy"
}'
```

- Continue to configure the system using the Administration guide.

- Configure a reverse proxy (optional for Docker Registry).

- Deploy an optional Nginx server.

- Logs - FluentD, Prometheus, Grafana, check https://github.com/jfrog/log-analytics-prometheus

- Enable metrics

- Plugins

- Backups (PostgreSQL's backup and restore, Cloud Storage, PVC, velero)

- Use external PostgreSQL database instead of built-in (https://www.jfrog.com/confluence/display/JFROG/PostgreSQL), set in Helm chart

  ```
  postgresql.postgresqlPassword
  postgresql.enabled=false
  ```

- Create GCP Cloud Storage bucket for artifacts:

  - Configure GCP service account
    - `artifactory.persistence.redundancy` and `artifactory.persistence.type`, use Google Storage bucket
    - Check `binarystore.xml` configuration
  - Create PVC (required as temporary eventual storage in cases of loss of connection to the external storage or if the Artifactory pod crashes)


- Expose Artifactory with Ingress
  - Contour ingress controller
    - point ssl to certificate
    - automatic creation/retrieval of TLS certificates (for example, by using a cert-manager)
    - configure ingress in chart
      - creating an Ingress Object `artifactory-ingress-values.yaml`
      - use  Ingress Annotations if needed
      - establishing TLS and Adding Certificates for Artifactory

- Configure SAML
  - okta
  - saml auth method



### Product Configuration

After installing and before running Artifactory, you may set the following configurations:

- Check `system.yaml` (`values.yaml` in Helm chart).
- First configure external database, and then start Artifactory.
- Modify JVM parameters.
- Additional settings: customizing ports, joinKey (join.key), masterKey (master.key).
- Configure filestore (S3, GCP Cloud Storage etc.)
- NetworkPolicy
  - Resource requests and limits to Artifactory, Nginx, etc.
  - Review all settings in `values.yaml`
  - Check all Artifactory settings in `values.yaml`
    - Java memory parameters should be set to match the allocated resources with `artifactory.javaOpts.xms` and `artifactory.javaOpts.xmx`, `artifactory.extraJavaOpt`
  - Customizing ports




### Install XRay (Helm chart)

> https://github.com/jfrog/charts/tree/master/stable/xray
> https://www.jfrog.com/confluence/display/JFROG/Installing+Xray

TODO



### Install Mission Control (Helm chart)

TODO



### Uninstalling Artifactory

Uninstall Artifactory using the following command. Deleting Artifactory using the commands below will also delete your data volumes and you will lose all of your data. You **must** back up all this information before deletion.

```sh
# helm v2
helm uninstall artifactory && ``sleep` `90 && kubectl delete pvc -l app=artifactory

# helm v3
helm delete artifactory --namespace artifactory
```

Next, delete the storage bucket and SQL database.

```sh
gsutil ``rm` `-r gs:``//artifactory` `gcloud sql instances delete artifactory
```
