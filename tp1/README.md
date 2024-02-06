# TP1

## Database
### QUESTION 1 - 1
Dockerfile: 

```
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd \
   PGDATA=/var/lib/postgresql/data/db-files/

COPY CreateScheme.sql /docker-entrypoint-initdb.d
COPY InsertData.sql /docker-entrypoint-initdb.d
```
(j'ai du ajouter PGDATA car sinon il y avait des conflits visiblement ...)
s
Je peux lancer la commande `docker build -t sspina69/tp1-postgres .`

Le container est maintenant créé grâce à l'image du Dockerfile. Je peux maintenant le lancer : 

`docker run -d -p 5432:5432 --network app-network --name database2 -v C:\Users\Sandro\Documents\CPE\S8\devops\tp1:/var/lib/postgresql/data sspina69/tp1-postgres`

-> -d pour run en detached mode, j'expose le port 5432 et je donne le nom database, le --network est utile pour que les container adminer et sspina69/tp1-postgres puissent communiquer. 
Je peux vérifier que mon container est bien en train de run comme ceci: 
`docker ps`
j'obtiens 
```
# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED          STATUS          PORTS                                           NAMES
a98de42f70d9   sspina69/tp1-postgres       "docker-entrypoint.s…"   9 minutes ago    Up 9 minutes    0.0.0.0:5432->5432/tcp                          database2
```

pour adminer: 
`docker run -p 8090:8080 --net=app-network --name=adminer -d adminer`

### Question 1 - 2

```
FROM maven:3.8.6-amazoncorretto-18 AS myapp-build # pull de l'image maven
ENV MYAPP_HOME /opt/myapp # on set la variable d'environnement si on veut la réutiliser plus tard
WORKDIR $MYAPP_HOME # on se place dans le dossier correspondant à cette var d'env
COPY ./demo.spring-api/pom.xml . # on place le pom.xml dans le dossier courant du container
COPY src ./src # pareil avec le src
RUN mvn package -DskipTests # mvn package va générer un jar autoportant avec toutes 

# Run
FROM amazoncorretto:17 # openjdk subdependency
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar # le jar qui a été généré par le maven package est run
```

On a besoin d'un multistage build car cela permet d'optimiser les ressources

### ayant été absent le premier jour, je n'ai pas eu à continuer le tp01

### Question 2 - 1

Les testcontainers: Il s'agit d'une technologie permettant de créer des environnements de test isolés et reproductibles pour les applications. ça peut être utilisé pour lancer une instance d'une base d données 