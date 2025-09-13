pipeline {
    agent any
    environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_REGISTRY = "jyauyor"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        DOCKER_REGISTRY_ENVIRONMENT = "desarrollo"
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
                   sh "pwd"
                   def jarName = sh(script: "ls target/*.jar | head -1", returnStdout: true).trim()
                   echo "*****JarName ${jarName}"
                    writeFile file: 'Dockerfile', text:"""
                    FROM eclipse-temurin:21-jdk-alpine
                    ADD ${jarName} /app/service.jar
                    WORKDIR /app
                    ENTRYPOINT ["java", "-jar", "/app/service.jar"]
                    """
                   
                   sh "ls -ltr"
                   sh "docker build -t ${env.DOCKER_REGISTRY}/${env.DOCKER_REGISTRY_ENVIRONMENT}/app-microservice:${APP_VERSION} ."                   
               }
               
                withDockerRegistry(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "https://index.docker.io/v1/") {
                    //withCredentials([usernamePassword(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
                        sh "docker push ${env.DOCKER_REGISTRY}/${env.DOCKER_REGISTRY_ENVIRONMENT}/app-microservice:${APP_VERSION}"
                }
            }
                             
            //sh "docker push ${DOCKER_REGISTRY}/app-microservice:${env.APP_VERSION} "
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
