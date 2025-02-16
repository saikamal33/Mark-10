# Mark-10
This is for the Kubernet practice
# Poject-1

## Kubernetes Installation Using KOPS on EC2
Create an EC2 instance or use your personal laptop.
Dependencies required

Python3
AWS CLI
kubectl
### Install dependencies
~~~
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y python3-pip apt-transport-https kubectl
pip3 install awscli --upgrade
export PATH="$PATH:/home/ubuntu/.local/bin/"
~~~
### Install KOPS (our hero for today)
~~~
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
~~~
### Provide the below permissions to your IAM user. If you are using the admin user, the below permissions are available by default
AmazonEC2FullAccess
AmazonS3FullAccess
IAMFullAccess
AmazonVPCFullAccess
### Set up AWS CLI configuration on your EC2 Instance or Laptop.
~~~
Run aws configure
~~~
## Kubernetes Cluster Installation
Please follow the steps carefully and read each command before executing.

Create S3 bucket for storing the KOPS objects.
aws s3api create-bucket --bucket kops-abhi-storage --region us-east-1
Create the cluster
~~~
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-abhi-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro  --master-volume-size=8 --node-volume-size=8
~~~
### Important: Edit the configuration as there are multiple resources created which won't fall into the free tier.
~~~
kops edit cluster myfirstcluster.k8s.local
~~~
## Step 12: Build the cluster
~~~
kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-abhi-storage
~~~
This will take a few minutes to create............

After a few mins, run the below command to verify the cluster installation.

kops validate cluster demok8scluster.k8s.local


## kubectl cheat sheet 
~~~
https://kubernetes.io/docs/reference/kubectl/quick-reference/
~~~

# Project-2
## Automated CI/CD Pipeline for Kubernetes with jenkins
To manually deploy this app
we have a notejs webapp and the dependencised of it inside the package.json file. we can to run the docker file to create an image
~~~
docker build -t puyt:latest .
~~~
then
we can deploy the image in the kubernet using the deployments.
~~~
kubectl run -d --name=python_app -p 3000:3000 deployment.yml
~~~

we can also automate the process using the jenkins CI/CD Pipelines
Run the jenkins pipeline from git repo 


## Lauch an ArgoCD inside the minikube cluster
I have deployed the argocd namespace inside the running minikube cluster and opened the 8080 port for access it through the web UI
### Install ArgoCD in the Kubernetes Cluster
You can install ArgoCD in your Kubernetes cluster using kubectl.

Install ArgoCD: First, apply the ArgoCD installation manifests:
~~~
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
~~~
Expose ArgoCD API Server: You need to expose the ArgoCD API server so you can access it in your browser:
~~~
kubectl port-forward svc/argocd-server -n argocd 8080:443
~~~
Now, you can access the ArgoCD UI at https://localhost:8080.

### Login to ArgoCD
The default username is admin. You can find the password using the following command:
~~~
kubectl get pods -n argocd
kubectl describe secret argocd-initial-admin-secret -n argocd #if its not encripted
(or)
kubectl get secret argocd-initial-admin-secret -n argocd -o=jsonpath='{.data.password}' | base64 --decode #if its encripted
~~~
Look for the password field and copy it. Then login to the ArgoCD UI at https://localhost:8080 using admin as the username and the copied password.

4. Create a GitHub Repository for Your Application
Create a Git repository on GitHub (or GitLab) where you will store your Kubernetes manifests for your application.

