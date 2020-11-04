# Demo Environemnt

Add the cloud-init file to your user data when creating a VM. Alternatively Install the dependencies and run the ansible playbook in the ansible folder.

Note: VM should have at least 16GB of memory. 

Add a host entry to your client like the following

`<VM IP> cd.demo.local kibana.demo.local`

You should now be able to deploy your pipelines using ArgoCD.

## Namespaces

If you are going to deploy your pipelines in separate namespaces you will need two secrets, one for the elastic password, and one for the elastic TLS certificate.
Example creating the certificate

`kubectl create secret generic -n NEWNAMESPACE elastic-ca-cert --from-literal="ca.crt=$(kubectl get secret quickstart-es-http-certs-public -o "jsonpath={.data['ca\.crt']}" | base64 -d)"`

Example creating the password

`kubectl create secret generic -n syslog elastic-user --from-literal="password=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')"`
