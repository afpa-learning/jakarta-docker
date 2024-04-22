# First stage: complete build environment
FROM maven:3.9.4-eclipse-temurin-17-alpine AS builder
# Configuration du répertoire de travail dans le conteneur
WORKDIR /app
# Copie des fichiers de configuration maven (à tester en le supprimant)
COPY ./settings/maven/settings.xml /root/.m2/settings.xml
# Copie du code source + pom.xml de l'application dans le conteneur
COPY . .
# Compilation de l'application et packaging en .war !
RUN mvn clean package
# Fin de l'étape de build

# Déploiement d'un Tomcat accueillant l'application
FROM tomcat:10.1.13
# Ouverture du port pour communication avec le monde extérieur
EXPOSE 8080
RUN mkdir /usr/local/tomcat/webapps/manager
RUN cp -r /usr/local/tomcat/webapps.dist/manager /usr/local/tomcat/webapps/manager
# Ajout d'utilisateur pour administration Tomcat
COPY ./settings/tomcat/tomcat-users.xml /usr/local/tomcat/conf/tomcat-users.xml
# Ajout du fichier de configuration serveur
COPY ./settings/tomcat/server.xml /usr/local/tomcat/conf/server.xml
# Paramétrage du dossier de travail
WORKDIR /webapps
# Copie de l'archive de l'application dans le dossier de deploiement du Tomcat
# la variable d'environnement $CATALINA_HOME fait référence à "/usr/local/tomcat/webapps/" 
COPY --from=builder /app/target/*.war $CATALINA_HOME/webapps
# Début du ronronnement
CMD ["catalina.sh", "run"]

