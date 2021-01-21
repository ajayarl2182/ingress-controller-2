---
title: Sample Tutorial
description: This is a sample tutorial to guide user on creating his own tutorial in md.User should follow exact syntax given below to render in user guide
---

### Introduction:
What is Ingress?
Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.
Here is a simple example where an Ingress sends all its traffic to one Service:

An Ingress may be configured to give Services externally-reachable URLs, load balance traffic, terminate SSL / TLS, and offer name-based virtual hosting. An Ingress controller is responsible for fulfilling the Ingress, usually with a load balancer, though it may also configure your edge router or additional frontends to help handle the traffic.
An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet typically uses a service of type Service.Type=NodePort or Service.Type=LoadBalancer.


Prerequisites:

You must have an Ingress controller to satisfy an Ingress. Only creating an Ingress resource has no effect.
You may need to deploy an Ingress controller such as ingress-nginx. You can choose from a number of Ingress controllers.
Ideally, all Ingress controllers should fit the reference specification. In reality, the various Ingress controllers operate slightly differently.
Lab- Prerequisite

For this labs you will need access to a kubernetes cluster. There is already kubernetes setup in this lab environment. You can use it or you can use your own kubernetes cluster by following procedure given below.

#Provision a Kubernetes Cluster

Note: Execute below steps if you have Kubernetes cluster on the IBM Cloud else skip this section. Steps of this lab will be executed by default on the Kubernetes setup installed on the attached terminal.

Login to IBM Cloud and create a Kubernetes cluster if you haven't already. Follow this link for creating a cluster.

Log in to the IBM Cloud CLI . You can either login with Option 1 or Option 2 (If your id is federated):

Option 1: Use this for normal login:

ibmcloud login
Enter your IBM Cloud credentials when prompted. You will find output similar like below. As an example we have used openlabs account details , you need to enter your own registered email id and password:

API endpoint: cloud​.ibm.com

Email> openlabs@ibm.com

Password>
Authenticating...
OK

Targeted account Openlabs's Account (80d297103c56406aa783749878e0e465b9bf)

Targeted resource group Default


Select a region (or press enter to skip):
1. au-syd
2. jp-tok
3. kr-seo
4. eu-de
5. eu-gb
6. us-south
7. us-east
Enter a number> 6
Targeted region us-south


API endpoint:      https​://cloud.ibm.com
Region:            us-south
User:              openlabs@ibm.com
Account:           Openlabs's Account (80d297103c56406aa783749878e0e465b9bf)
Resource group:    Default
CF API endpoint:
Org:
Space:

Tip: If you are managing Cloud Foundry applications and services
- Use 'ibmcloud target --cf' to target Cloud Foundry org/space interactively, or use 'ibmcloud target --cf-api ENDPOINT -o ORG -s SPACE' to target the org/space.
- Use 'ibmcloud cf' if you want to run the Cloud Foundry CLI with current IBM Cloud CLI context.


New version 0.20.0 is available.
Release notes: https​://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/tag/v0.20.0
TIP: use 'ibmcloud config --check-version=false' to disable update check.
Option 2:

Note: If you have a federated ID, use "ibmcloud login --sso" to log in to the IBM Cloud CLI. Use the provided URL in your CLI output to retrieve your one-time passcode. You have a federated ID if login fails without the --sso and succeeds with the --sso option.

ibmcloud login --sso
Using --sso you will see output similar like below: In this you need to select account and region if you have access to multiple account and regions. As an example we have used openlabs account details , you need to use your own registered email id and password to get One Time Code:

ibmcloud login --sso
API endpoint: https​://cloud.ibm.com

Get One Time Code from https​://identity-3.us-south.iam.cloud.ibm.com/identity/passcode to proceed.
Open the URL in the default browser? [Y/n]> y
One Time Code >
Authenticating...
OK

Select an account:
1. IBM (80d297103c56406aa783749878e0e465b9bf)
2. IBM (3a4766a7bcab032d4ffc980d360fbf23) <-> 338150
Enter a number> 1
Targeted account IBM (80d297103c56406aa783749878e0e465b9bf)

Targeted resource group Default


Select a region (or press enter to skip):
1. au-syd
2. jp-osa
3. jp-tok
4. kr-seo
5. eu-de
6. eu-gb
7. us-south
8. us-south-test
9. us-east
Enter a number> 7
Targeted region us-south


API endpoint:      https​://cloud.ibm.com
Region:            us-south
User:              openlabs@ibm.com
Account:           IBM (80d297103c56406aa783749878e0e465b9bf)
Resource group:    Default
CF API endpoint:
Org:
Space:

Tip: If you are managing Cloud Foundry applications and services
- Use 'ibmcloud target --cf' to target Cloud Foundry org/space interactively, or use 'ibmcloud target --cf-api ENDPOINT -o ORG -s SPACE' to target the org/space.
- Use 'ibmcloud cf' if you want to run the Cloud Foundry CLI with current IBM Cloud CLI context.


New version 0.20.0 is available.
Release notes: https​://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/tag/v0.20.0
TIP: use 'ibmcloud config --check-version=false' to disable update check.
If you have a api key then, use "ibmcloud login --apikey <api_key>" to log in to the IBM Cloud CLI

#Copy below command and replace <​CLUSTER.name> with your cluster name and execute it to get your cluster details.

ibmcloud ks cluster ls | grep  <​CLUSTER.name>
You should see output similar to the following. Wait until state value becomes normal

Name         ID                     State    Created          Workers   Location   Version       Resource Group Name   Provider
<​CLUSTER.name>   <​CLUSTER.id>   normal   41 minutes ago   1         <​CLUSTER.dataCenter>      1.14.8_1536   Default               classic
Once the cluster is provisioned, the kubernetes client CLI kubectl needs to be configured to talk to the provisioned cluster.

#Copy below command and replcase <​CLUSTER.name> to your cluster name and execute it to get value KUBECONFIG

ibmcloud ks cluster config --cluster <​CLUSTER.name>
You should see output similar to the following.

OK
The configuration for <​CLUSTER.name> was downloaded successfully.
Export environment variables to start using Kubernetes.
export KUBECONFIG=/home/student/.bluemix/plugins/container-service/clusters/<​CLUSTER.name>/kube-config-<​CLUSTER.dataCenter>-<​CLUSTER.name>.yml
Copy below command and replace <​CLUSTER.name> with you cluster name and <​CLUSTER.dataCenter> with your cluster data center and execute to set the KUBECONFIG environment variable based on the output of the command.

This will make your kubectl client point to your new Kubernetes cluster.

export KUBECONFIG=/home/student/.bluemix/plugins/container-service/clusters/<​CLUSTER.name>/kube-config-<​CLUSTER.dataCenter>-<​CLUSTER.name>.yml
Once your client is configured, you are ready to deploy your application

### Create and Manage Ingress Controller
Deploy a sample Hello App using the below commands 

kubectl create deployment hello-app --image=gcr.io/google-samples/hello-app:1.0

Kubectl get pod

Pods are deployed in default namespace

Once the hello-app deployed expose the app to connect from cluster from port 8080 on default namespace

kubectl expose deployment hello-app  --port=8080

Kubectl get svc

Service deployed on default namespace

Deploy ingress controller using node port service to connect from outside cluster

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/baremetal/deploy.yaml

Ingress controller will deployed on ingress-nginx namespace


Kubectl get pods -n ingress-nginx

Kubectl get svc -n ingress-nginx


Sample hello-app ingress example.

cat << EOF | kubectl apply -f -   
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
  name: hello-app
spec:
  rules:
  - http:
      paths:
      - path: /hello
        backend:
          serviceName: hello-app
          servicePort: 8080
EOF

Hello app ingress is creating on default namespace.

Kubectl get ing

It will display the ingress address to connect from browser

We are using node port service so we can able to access the site using node port of the ingress controller which we can get it from 

Kubectl get svc -n ingress-nginx  eg: 80:31688/TCP,443:3118

http://<EXTERNAL_IP>:31688/hello



### What happens during an update

So what can you expect from your application’s availability during your cloud version upgrade?
Generally, you can expect the following three stages for your containerized application during an update:
* Preparing the cluster for upgrade.
* Upgrading the cluster core components.
* Upgrading the cluster add-on components.
In the first stage of preparing the cluster for upgrade, the cluster data is normally backed up before the real upgrading process begins. In the second stage, core components will upgrade to a newer version. Using IBM Cloud Private as an example, components like apiserver, controller-manager, and scheduler will upgrade to a newer version. Generally, applications won't call the core components directly, so the first two stages won't affect your applications!
In the third stage, add-on components like Calico, KubeDNS, and the NGINX Ingress Controller are upgrading, as your applications rely on these components, so you can expect there will be some (minimal) outage during the add-on components upgrade portion.
Tips to implement ahead of an update
There are four places where outages can occur during an update.
1. Container
Besides the 3 upgrade stages I mentioned, you might also want to upgrade the container version, like Docker. Upgrading Docker will restart all the containers that are running on the host. This will affect your application availability and this is an outage that is hard to avoid.
2. Container network
Pod-to-pod communication depends on the stability of container network. Upgrading network components might affect your container network. The stability of the container network depends on your cloud cluster. Using IBM Cloud Private as an example, IBM Cloud Private uses Calico as the default Container Network Interface (CNI) plug-in, and there is no downtime in the container network during upgrade.
3. DNS
As the typical containerized application shows, pod internal communication depends on the cluster domain name service. To reach a zero downtime upgrade, DNS must achieve both a graceful shutdown and a rolling upgrade. Graceful shutdown ensures the processing request is finished before pods exist. For a rolling upgrade request, you need to have at least two pods in your instance and ensure that there is always a pod available during upgrade.
If the upgrade can’t achieve graceful shutdown and rolling upgrade, the outage can last a few seconds during a DNS upgrade. During the outage, internal calls to the pod by service name might fail.


![codestructure](_images/mvc-db-structure.png)

### Accordian what yiu learned

Simple text to demonstrate ul li :
- list text 1. what is Ingress controller
- list text 2. How to create and Manager Ingress controller
- list text 3. What happens during an update