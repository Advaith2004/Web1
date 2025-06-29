<role rolename="manager-gui">
<role rolename="admin-gui">
<user username="tomcat" password="tomcat" roles="manager-gui,admin-gui,managaer-script,manager-jmx,manager status"/>

git access token/password -  
   

3.
FROM tomcat:9.0
RUN rm -rf /usr/local/tomcat/webapps/*
COPY /target/sample.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080

Step 1: Create App 1 (Service Provider Folder in VS-CODE)
app1/app.py(File)

from flask import Flask
app = Flask(__name__)
@app.route(&#39;/&#39;)
def hello():
return &quot;Hello from App 1!&quot;
if __name__ == &#39;__main__&#39;:
app.run(host=&#39;0.0.0.0&#39;, port=5000)

app1/requirements.txt (file)
flask==3.0.0
app1/Dockerfile (file)
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD [&quot;python&quot;, &quot;app.py&quot;]
Step 2: Create App 2 (Service Consumer Folder in VS-CODE)
app2/app.py (file)
import requests
response = requests.get(&quot;http://app1:5000/&quot;)
print(&quot;Response from App 1:&quot;, response.text)

Dept of CS&amp;E, DSCE5
app2/requirements.txt (file)
requests==2.31.0
app2/Dockerfile(file)
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
CMD [&quot;python&quot;, &quot;app.py&quot;]

Step 3: Create Docker Compose File
docker-compose.yml
version: &#39;3.9&#39;
services:
app1:
build: ./app1
networks:
- app-network
ports:
- &quot;5000:5000&quot;
app2:
build: ./app2
networks:
- app-network
depends_on:
- app1
networks:
app-network:
driver: bridge




5.
provider "aws" {
    region="us-west-2"
}
resource "aws_instance" "ec2_machine" {
    ami="ami-07b0c09aab6e66ee9"
    instance_type="t2.micro"
    count=1
    tags={
        name="terra EC2"
    }
}
resource "aws_s3_bucket" "demo_bucket" {
    bucket="my-demo-bucket-1d22cs006-dsce-cse"
    tags={
        Name="upload-demo"
    }
}
resource "aws_s3_bucket_object" "text_file"{
    bucket=aws_s3_bucket.demo_bucket.bucket
    key="sample1.txt"
    source="./sample1.txt"
}


4.
**deployment.yaml**
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hw-deployment
spec:
  replicas: 2  
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hw-container
        image: chethanaravi/app1-k8s:latest
        ports:
        - containerPort: 5000

  **service.yaml**
  apiVersion: v1
kind: Service
metadata:
  name: hello-world
spec:
  type: NodePort    
  selector:
    app: hello-world
  ports:
    - port: 5000        
      targetPort: 5000
