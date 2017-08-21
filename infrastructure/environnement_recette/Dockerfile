FROM tomcat:8.0.33-jre8

COPY gameoflife-web-1.85.war /usr/local/tomcat/webapps/

ADD settings.xml /usr/local/tomcat/conf/
ADD tomcat-users.xml /usr/local/tomcat/conf/

CMD ["catalina.sh", "run"]
