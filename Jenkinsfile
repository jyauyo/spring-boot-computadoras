pipeline {
    agent any
    environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        REPO_GIT_APP = ""
        APP_VERSION = ""
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
            

            ///env.REPO_GIT_APP = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
            
            
            steps {
                checkout scm
                // Clonar el repositorio desde GitHub
                //env.REPO_GIT_APP = "https://github.com/jyauyo/spring-boot-computadoras.git"
                //git url: 'https://github.com/jyauyo/spring-boot-computadoras.git', branch: "${params.BRANCH}"
                //git url: "${env.REPO_GIT_APP}", branch: "${params.BRANCH}"
                script {
                    //def urlRepoGit = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                    //env.REPO_GIT_APP = urlRepoGit
                    //env.REPO_GIT_APP = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                    //echo "**** Repositorio GIT: ${env.REPO_GIT_APP}"

                    //git url: "${env.REPO_GIT_APP}", branch: "${params.BRANCH}"

                    //def pom = readMavenPom file: 'pom.xml'

                    // Access the version property
                    //def mavenVersion = pom.version

                    // Print the version to the console
                    //echo "***** Maven Project Version: ${mavenVersion}"
                    //env.APP_VERSION = mavenVersion

                    //echo "***** Version: ${env.APP_VERSION}"
                    
                    //env.REPO_GIT_APP = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                    env.APP_VERSION = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                    echo "***** Version: ${env.APP_VERSION}"

                    env.APP_VERSION = '1.1.1'

                    echo "***** Version: ${env.APP_VERSION}"
                    //println env.REPO_GIT_APP
                    //println env.APP_VERSION
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
                   echo "*****JarName ${jarName}"
                    writeFile file: 'Dockerfile', text:"""
                        from eclipse-temurin:21-jre
                        copy ${jarName} /app/service.jar
                        ENTRYPOINT ["java", "-jar", "/app/service.jar"]
                    """
                    sh "docker build -t ${env.DOCKER_REGISTRY}/app-microservice:${env.APP_VERSION} ."                   
               }
               
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
