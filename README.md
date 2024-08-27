# Kubernetes_KOPs
## Kubernetes Installation Using KOPS on EC2

### Create an EC2 instance or use your personal laptop.

Dependencies required 

1. Python3
2. AWS CLI
3. kubectl

###  Install dependencies

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
### The command you provided is used to add a GPG key to your system's package manager, allowing it to verify packages from Google's package repository.
```

```
sudo apt-get update
sudo apt install python3-pip
sudo snap install kubectl --classic
kubectl version --client
```

```
### Install python3-venv
sudo apt-get install python3-venv
### Create and activate a virtual environment
python3 -m venv myenv
source myenv/bin/activate
### Install AWS CLI within the virtual environment
pip install awscli --upgrade
aws --version
```

```
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### Install KOPS 
### Download: Fetches the latest version of kops from GitHub.
### Make Executable: Sets the file as executable so it can be run as a command.
### Move to PATH: Places the binary in a directory that's in the system's PATH, making it accessible from anywhere in the terminal.

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Provide the below permissions to your IAM user. If you are using the admin user, the below permissions are available by default

1. AmazonEC2FullAccess
2. AmazonS3FullAccess
3. IAMFullAccess
4. AmazonVPCFullAccess

### Set up AWS CLI configuration on your EC2 Instance or Laptop.

Run `aws configure`

## Kubernetes Cluster Installation 

### Create S3 bucket for storing the KOPS objects.

```
aws s3api create-bucket --bucket kops-palash-storage1 --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
```

### Create the cluster 

```
kops create cluster \
  --name=demok8scluster.k8s.local \
  --state=s3://kops-palash-storage1 \
  --zones=ap-south-1a \
  --node-count=1 \
  --node-size=t2.micro \
  --control-plane-size=t2.micro \
  --control-plane-volume-size=8 \
  --node-volume-size=8
```

### Important: Edit the configuration as there are multiple resources created which won't fall into the free tier.

```
kops get clusters --state=s3://kops-palash-storage1
kops edit cluster demok8scluster.k8s.local --state=s3://kops-palash-storage1

```

Step 12: Build the cluster

```
kops update cluster demok8scluster.k8s.local --state=s3://kops-palash-storage1 --yes
```

This will take a few minutes to create............

After a few mins, run the below command to verify the cluster installation.

```
kops validate cluster --state=s3://kops-palash-storage1

```
### Delete Cluster (if needed)
```
kops delete cluster demok8scluster.k8s.local --state=s3://kops-palash-storage1 --yes

```
