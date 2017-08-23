FROM tomcat:8.0.33-jre8

ADD gameoflife-web-2.1.war /usr/local/tomcat/webapps/

ADD settings.xml /usr/local/tomcat/conf/
ADD tomcat-users.xml /usr/local/tomcat/conf/

CMD ["catalina.sh", "run"]
