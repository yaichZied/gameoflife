FROM tomcat:8.0.33-jre8

ADD gameoflife-web-1.84.war /usr/local/tomcat/webapps/

ADD settings.xml /usr/local/tomcat/conf/
ADD tomcat-users.xml /usr/local/tomcat/conf/

CMD ["catalina.sh", "run"]
