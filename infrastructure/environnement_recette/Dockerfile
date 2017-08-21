FROM tomcat:8.0.33-jre8

ADD /var/lib/jenkins/workspace/oflife_BranchPipelineEditor-56OXKSGJJCF2UZ5TSWSHNRL6Z5OI3CY2TJF5XAYXYJHHO6CBSHDA/gameoflife-web-1.82.war /usr/local/tomcat/webapps/

ADD settings.xml /usr/local/tomcat/conf/
ADD tomcat-users.xml /usr/local/tomcat/conf/

CMD ["catalina.sh", "run"]
