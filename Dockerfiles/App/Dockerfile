FROM openjdk:8 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone https://github.com/Chiku0306/javaspringbootpoc.git
RUN cd javaspringbootpoc && mvn install

FROM tomcat:8-jre11

#remove default 
RUN rm -rf /usr/local/tomcat/webapps/*

#copy build 
COPY --from=BUILD_IMAGE javaspringbootpoc/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war


EXPOSE 8080
CMD ["catalina.sh", "run"]
