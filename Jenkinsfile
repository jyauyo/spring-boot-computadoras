def algunavariable

pipeline {
    agent any
    environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        //DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_REGISTRY = "jyauyor"
        DOCKER_URL = "https://index.docker.io/v1/"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        DOCKER_REGISTRY_ENVIRONMENT = ""
    }
    //agent {
    //    docker {
    //        image 'maven:3.6.3-jdk-13'
    //        args '-v /tmp/maven:/home/jenkins/.m2 -e MAVEN_CONFIG=/home/jenkins/.m2'
    //    }
    //}
   
    tools {
        maven 'Maven Apache'
    }

    parameters {
        string(name: 'BRANCH', defaultValue: 'develop', description: '')
    }
    
    stages {
        stage('Checkout') {
            
            steps {

                script {
                    def pom = readMavenPom file: 'pom.xml'
                    echo "Project Version: ${pom}"
                }
                
                checkout scm
                
                script {                    
                    
                    APP_VERSION = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                    echo "***** Version: ${APP_VERSION}"

                }
                
            }
        }
        
        stage('Build') {
            steps {
                // Compilar el proyecto usando Maven
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {

           steps {
               script {

                   def jarName = sh(script: "ls target/*.jar | head -1", returnStdout: true).trim()
                   echo "***** JarName ${jarName}"

                   echo "***** Creating Dockerfile"
                   writeFile file: 'Dockerfile', text:"""
                   FROM eclipse-temurin:21-jdk-alpine
                   ADD ${jarName} /app/service.jar
                   WORKDIR /app
                   ENTRYPOINT ["java", "-jar", "/app/service.jar"]
                   """

                   //sh "docker build -t ${env.DOCKER_REGISTRY}${env.DOCKER_REGISTRY_ENVIRONMENT}/app-microservice:${APP_VERSION} ."
                   def projectName = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                   echo("***** Project Name: ${projectName}");
                   
                   def docker_registry_environment_ = "${env.DOCKER_REGISTRY_ENVIRONMENT}"
                   def docker_registry_complete = "${env.DOCKER_REGISTRY}"
                   echo("***** Docker Registry Pre: ${docker_registry_environment_}");
                   
                   if (docker_registry_environment_ != null && !docker_registry_environment_.isEmpty()) {
                       //docker_registry_complete = "${docker_registry_complete}/${docker_registry_environment_}".trim()
                   }
                   echo("***** Docker Registry Final: ${docker_registry_complete}");
                   
                   def dockerfile = 'Dockerfile'
                   def customImage = docker.build("${docker_registry_complete}/${projectName}:${APP_VERSION}", "-f ${dockerfile} .")

                   withCredentials([usernamePassword(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", usernameVariable: 'dockerHubUser', passwordVariable: 'dockerHubPassword')]){
                    //withDockerRegistry(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_URL}") {
                       echo  "${env.dockerHubPassword} | login --username ${env.dockerHubUser} --password-stdin  ${env.DOCKER_URL}"                   
                       //sh "docker push ${env.DOCKER_REGISTRY}${env.DOCKER_REGISTRY_ENVIRONMENT}/app-microservice:${APP_VERSION}"
                   }
                   echo "***** Publishing to Docker Registry: ${APP_VERSION}"
                   customImage.push()
               }
               echo "***** Cleaning ..."
               sh 'mvn clean'
               
            }
        }
    }
    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
