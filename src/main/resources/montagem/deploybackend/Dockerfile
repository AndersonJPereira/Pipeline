FROM tomcat:8.5.50-jdk11-openjdk

ARG WAR_FILE

ARG CONTEXT_NAME

COPY ${WAR_FILE} /usr/local/tomcat/webapps/${CONTEXT_NAME}.war