# Demo Environemnt

## Install

* A CentOS8 VM with 16GB RAM, 30GB Disk

### Option 1)

Add the cloud-init file to a *CentOS 8* VM on creation. 

The VM will update, install some dependencies and reboot. On reboot it will install the demo environment


### Option 2)

1. Disable SELinux

2. Install EPEL
``` yum -y install epel-release ```

3. Install some dependencies
``` yum -y install git ansible wget ```

4. Update and reboot
``` 
yum -y update
reboot
```

5. Install the environment using ansible pull
```
ansible-pull -U https://github.com/dnhodgson/carbon_arc -i demo/ansible/hosts demo/ansible/local.yml
```

## Usage

After completing either of the 2 install options you should now have a working demo environment. Run the following to create a DEMO file in root home comtaining the usernames/passwords

```
cat << EOF >> /root/DEMO	
Argo Username: admin	
Argo Password $(/usr/local/sbin/kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2)	
Elastic Username: elastic	
Elastic Password: $(/usr/local/sbin/kubectl get secret -n demo quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')	
EOF
```

### Passwords

Use the following commands from the `root` account in your cluster to get the passwords for the root portals

Elastic:

&nbsp;&nbsp;&nbsp;&nbsp;username: elastic

&nbsp;&nbsp;&nbsp;&nbsp;password: `kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}'`
  
ArgoCD:

&nbsp;&nbsp;&nbsp;&nbsp;username: admin

&nbsp;&nbsp;&nbsp;&nbsp;password: `kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2`


### Web Portals

Add a host entry to your client like the following

`<VM IP> cd.demo.local kibana.demo.local`

You should now be able to deploy your pipelines using ArgoCD.

### ArgoCD

ToDo...

## Namespaces

If you are going to deploy your pipelines in separate namespaces you will need two secrets, one for the elastic password, and one for the elastic TLS certificate.
Example creating the certificate

`kubectl create secret generic -n NEWNAMESPACE elastic-ca-cert --from-literal="ca.crt=$(kubectl get secret quickstart-es-http-certs-public -o "jsonpath={.data['ca\.crt']}" | base64 -d)"`

Example creating the password

`kubectl create secret generic -n syslog elastic-user --from-literal="password=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')"`
