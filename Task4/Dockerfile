FROM tomcat

ARG sourceUrl=http://localhost:8081/nexus/content/repositories/releases/training/WebApp-$version.war

ARG destinationUrl=test_app
ARG destinationFile=$destinationUrl.war 

ADD $sourceUrl webapps/$destinationFile
