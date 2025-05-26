<role rolename="manager-gui">
<role rolename="admin-gui">
<user username="tomcat" password="tomcat" roles="manager-gui,admin-gui,managaer-script,manager-jmx,manager status"/>


   

3.
FROM tomcat:9.0
RUN rm -rf /usr/local/tomcat/webapps/*
COPY /target/sample.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
