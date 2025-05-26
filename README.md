<role rolename="manager-gui">
<role rolename="admin-gui">
<user username="tomcat" password="tomcat" roles="manager-gui,admin-gui,managaer-script,manager-jmx,manager status"/>


   

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
