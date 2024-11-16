+ <b>Author: Moole Muralidhara Reddy</b></br>
+ <b>Email:</b> techworldwithmurali@gmail.com</br>
+ <b>Website:</b> techworldwithmurali.com , devopsbymurali.com</br>
+ <b>Description:</b> Below are the steps outlined for manually Deploy to EKS fetching image from DockerHub.</br>

## Manually - Deploy to EKS fetching image from DockerHub.

### Prerequisites:
+ Git is installed
+ Maven is installed
+ Docker is installed
+ DockerHub repository is created
+ AWS EKS is created
+ IAM User is created
+ kubectl is installed
+ aws cli is installed

### Step 1: Clone the repository
  
```xml
  github url: https://github.com/techworldwithmurali/java-application.git
```
### Step 2: build the code
```xml
mvn package
```
### Step 3: Create the repository in DockerHub
```xml
Repository Name: web-application
```
### Step 4: Write the Dockerfile
```xml
FROM tomcat:9
RUN apt update
WORKDIR /usr/local/tomcat
ADD target/*.war webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```
### Step 5: Build and tag the Docker image
```xml
docker build . --tag web-application:latest
docker tag web-application:latest mmreddy424/web-application:latest
```
### Step 6: Login to DockerHub in local
```xml
docker login
```
### Step 7: Push the docker image to DockerHub
```xml
docker push mmreddy424/web-application:latest
```
### Step 8: Verify whether docker image is pushed or not in DockerHub
### Step 9 : Write the Kubernetes Deployment and Service manifest files.
##### deployment.yaml
```xml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-application
        image: web-app:1
        ports:
        - containerPort: 8080
```
##### service.yaml
```xml

apiVersion: v1
kind: Service
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  type: NodePort
  ports:  
    - port: 8080
      targetPort: 8080
  selector:
    app: web-app
```
### Step 10: Update the Dockerhub image in deployment.yaml
### Step 11: Configure  to the AWS CLI using Access key ID & Secret access key
```xml
aws configure
```
### Step 12: Connect to the AWS EKS Cluster
```xml
aws eks update-kubeconfig --name dev-cluster --region us-east-1
````
### Step 13: Apply the Kubernetes manifest files
```
kubectl apply -f .
```
### Step 14: Verify wether pods are running or not
```
kubectl get pods -A
```
### Step 15: Create a secret file for Dockerhub credenatils
```xml
kubectl create secret docker-registry dockerhubcred \
--docker-server=https://index.docker.io/v1/ \
--docker-username=mmreddy424 \
--docker-password=Docker@123 \
--docker-email=techworldwithmurali@gmail.com \
--namespace dev
```
```xml
  imagePullSecrets:
  - name: dockerhubcred

```
### Step 16: Access java application through NodePort.
```
http://Node-IP:port/web-application
```


#### Congratulations. You have successfully Deployed the java application in Kubernetes(AWS EKS).
