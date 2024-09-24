# Automated Deployment with Jenkins, Docker, Kubernetes, and Git

![34c4635e-c13e-4d95-87cd-532ef573a67d.png](34c4635e-c13e-4d95-87cd-532ef573a67d.png)

![image2%205.png](image2%205.png)

### Dockerize the production app

![image3%204.png](image3%204.png)

![image4%203.png](image4%203.png)

![image5%203.png](image5%203.png)

### **Use Kubernetes to deploy this app**

![image6%204.png](image6%204.png)

![image7%204.png](image7%204.png)

![image8%204.png](image8%204.png)

![image9%203.png](image9%203.png)

![image10%203.png](image10%203.png)

### Create a Jenkins freestyle pipeline to deploy the latest version of the app the moment there is a change on the GitHub repo

Webhooks typically require a publicly accessible domain name to receive incoming requests, need to run pipeline manually here.

### **Test the website at port 85, to verify the app is working. If the test case is passed, the the application will be deployed on Kubernetes.**

```bash
nilesh@ubuntu:~/DevOpsProfessional$ kubectl apply -f service.yaml
The Service "apache-service" is invalid: spec.ports[0].nodePort: Invalid value: 85: provided port is not in the valid range.
The range of valid ports is 30000-32767
```

### **Create a new pipeline to deploy the application on Kubernetes using your custom image of the application from Docker hub. Make sure your application is visible as a NodePort Service.**

![image11%202.png](image11%202.png)

![image12%201.png](image12%201.png)

![image13.png](image13.png)

![image14.png](image14.png)

### **Jenkins Build Steps**

```bash
#!/bin/bash
# make sure minikube is running at backend
# Build Docker image

docker build -t nileshmuthal1317/my_docker_image_001:latest .

# Login to Docker Hub using Jenkins credentials

echo $DOCKER_HUB_PASSWORD | docker login -u nileshmuthal1317 --password-stdin

# Push Docker image to Docker Hub

docker push nileshmuthal1317/my_docker_image_001:latest

# Set kubeconfig environment variable

export KUBECONFIG=/var/lib/jenkins/.kube/config

# Apply Kubernetes manifests

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml

# Verify deployment status

kubectl get deployment
kubectl get pods
kubectl get services
kubectl get ingress
```

![image15.png](image15.png)

![image16.png](image16.png)

### Authenticate Jenkins to use MiniKube

We need to copy certain files from the `.minikube` directory to Jenkins and update their permissions accordingly.

![image17.png](image17.png)

![image18.png](image18.png)

![image19.png](image19.png)

![image20.png](image20.png)

### **Create the required infrastructure using Terraform and Install Java on machines using Configuration Management Tool(Ansible)**

### **Terraform**

![image21.png](image21.png)

![image22.png](image22.png)

![image23.png](image23.png)

### **Ansible**

![image24.png](image24.png)

![image25.png](image25.png)

![image26.png](image26.png)