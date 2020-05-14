# LAB: Create A Pipeline For A Java App
In this lab, we are building a continuous delivery (CD) pipeline. We are using a very simple application written in Go. For the sake of simplicity, we are going to run only one type of test against the code. The prerequisites for this lab are as follows:

This repo consists of a Java Hello World application. It shows:

Run & test the application from CLI

Create a Docker image, run the container and test it

Create Kubernetes deployment and test it

Let’s get started!

A running Jenkins instance. This could be a cloud instance, a virtual machine, a bare metal one, or a docker container. It must be publicly accessible from the internet so that the repository can connect to Jenkins through web-hooks.

Image registry: you can use Docker Registry, a cloud-based offering like ECR or GCR, or even a custom registry.

An account on GitHub. 

The pipeline can be depicted as follows:

Create a CI/CD pipeline with Kubernetes and Jenkins

The Application Files
Our sample application will respond with ‘Hello World’ to any GET request. Create a new file called App.java and Since we are building a CD pipeline, we should have some tests in place. Our code is so simple that it only needs one test case which is AppTest.java = Source code can be found in src.

The Docker File where we package our Application

The Dockerfile is a multistage one to keep the image size as small as possible. The Dockerfile is avaible in repository to view.

The Service
Since we are using Kubernetes as the platform on which we host this application, we need at least a service and a deployment.

The deployment
The application itself, once dockerized, can be deployed to Kubernetes through a Deployment resource. The deployment.yml file available in repositoty. The most interesting thing about this deployment definition is the image part. Instead of hardcoding the image name and tag, we are using a variable. Later on, we shall see how we can use this definition as a template for Ansible and substitute the image name (and any other parameters of the deployment) through the command line arguments. 

The Playbook

In this lab, we are using Ansible as our deployment tool. There are many other ways to deploy Kubernetes resources including Helm Charts, but I thought Ansible is a much easier option. Ansible uses playbooks to organize its instructions. Our playbook.yml file placed in repository.

Ansible already includes the k8s module for handling communication with the Kubernetes API server. So, we don’t need kubectl installed but we do need a valid kubeconfig file for connecting to the cluster.


Step 2 :-

Install Jenkins, Ansible, and Docker
Let’s install Ansible and use it to automatically deploy a Jenkins server and Docker runtime environment.

Step 03: Configuring Jenkins User To Connect To The Cluster
Jenkins runs with jenkins user /home/jenkins/.kube/config kube config file is placed in above path.jenkins user have access to kubernetes cluster where it will deploy the apps.

Step 04: Create The Jenkins Pipeline Job

Create a new Jenkins job and select the Pipeline type. We used the Poll SCM as the build trigger; setting this option instructs Jenkins to check the Git repository on a periodic basis (every minute as indicated by * * * * *). If the repo has changed since the last poll, the job is triggered.
In the pipeline itself, we specified the repository URL and the credentials. The branch is master.
In this lab, we are adding all the job’s code in a Jenkinsfile that is stored in the same repository as the code.

Step 05: Configure Jenkins Credentials For GitHub and Docker Hub

Go to /credentials/store/system/domain/_/newCredentials and add the credentials to both targets. Make sure that you give a meaningful ID and description

Step 06: Create the Jenkinsfile

The Jenkinsfile is what instructs Jenkins about how to build, test, dockerize, publish, and deliver our application. Jenkinsfile is available in repository to view.

kubernetes cluster creation Please follow cluster creation steps mentioned in the file kubernetes_cluster

Horizontal pod auto scaler deployment kubectl apply -f kubernetes_app_HPA.yaml

It will autoscale number of pods based on memory utilization

pod distrution budget deployment kubectl apply -f kubernetes_app_PDB.yaml

application service deployment kubectl apply -f kubernetes_app_service.yaml
