FROM maven:3.8.6-amazoncorretto-18 AS myapp-build
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY ./simple-api-student-main/pom.xml .
COPY ./simple-api-student-main/src ./src
ENV jdbcUrl=jdbc:postgresql://database2:5432/db
ENV dbUsername=usr
ENV dbPwd=pwd
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar