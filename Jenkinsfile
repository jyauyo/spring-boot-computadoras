pipeline {
    agent any
    environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
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

    //parameters {
    //    string(name: 'BRANCH', defaultValue: 'develop', description: '')
    //}
    
    stages {
        stage('Checkout') {
            

            env.REPO_GIT_APP = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
            println env.REPO_GIT_APP
            
            steps {
                // Clonar el repositorio desde GitHub
                //git url: 'https://github.com/jyauyo/spring-boot-computadoras.git', branch: "${params.BRANCH}"
                git url: "${env.REPO_GIT_APP}", branch: "${params.BRANCH}"
                env.APP_VERSION = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                println env.APP_VERSION
            }
        }
        stage('Build') {
            steps {
                // Compilar el proyecto usando Maven
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {
            def jarName = sh(script: "ls target/*.jar | head -1", returnStdout: true).trim()
            writeFile file: 'Dockerfile', text:"""
                from eclipse-temurin:21-jre
                copy ${jarName} /app/service.jar
                ENTRYPOINT ["java", "-jar", "/app/service.jar"]
            """
            sh "docker build -t ${env.DOCKER_REGISTRY}/app-microservice:${env.APP_VERSION} ."

           steps {
                withDockerRegistry(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "https://index.docker.io/v1/") {
                    sh "docker push ${env.DOCKER_REGISTRY}:${env.APP_VERSION}"
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
