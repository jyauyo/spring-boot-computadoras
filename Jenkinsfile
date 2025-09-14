@Library("my-shared-library@develop") _
import sharedlib.DockerJenkinUtils
def utilsDocker = new DockerJenkinUtils(this)

def projectName
def nroPase
def projectNameGit

pipeline {
    agent any
    environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        //DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_REGISTRY = "jyauyor"
        DOCKER_URL = "https://index.docker.io/v1/"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        GITHUB_CREDENTIALS_ID = "github-credentials-jyauyo"
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
                    //projectNameGit = scm.getUserRemoteConfigs()[0].getUrl()
                    projectName = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                    echo("***** Project Name: ${projectName}");
                    
                    def pom = readMavenPom file: 'pom.xml'
                    nroPase = pom.properties.nroPase
                    echo "***** NroPase: ${nroPase}"


                    sh """ 
                    #!/bin/bash
                    pwd
                    cd ..
                    rm -rf ${nroPase}
                    mkdir ${nroPase}
                    pwd
                    """
                    
                    dir("../${nroPase}") {
                        withCredentials([usernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                            sh 'git config --global user.email "jenkins@example.com"'
                            sh 'git config --global user.name "Jenkins"'
                            sh "git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/jyauyo/${projectName}.git"
                            
                            dir("${projectName}") {
                                sh "git checkout ${BRANCH}"
                                sh "git pull"
                                echo "rama clonada ${BRANCH}"
                            }
                        }
                    }
                }
                
                //checkout scm                
                
                script {                    
                    
                    APP_VERSION = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                    echo "***** Version: ${APP_VERSION}"
                    utilsDocker.build(projectName: "${projectName}", version: "${APP_VERSION}")
                }
                
            }
        }

        stage('Crear y Commitear') {
            when {
                expression { false }
            }
            steps {
                script {
                    def newBranchName = "feature/${nroPase}"
                    def commitMessage = "Agrega la funcionalidad XYZ en la rama" 
                    sh "pwd"
                    //sh "cd .."
                    //sh "mkdir clonacion"
                    //sh "cd clonacion"                   
                    dir("../${nroPase}") {
                        dir("${projectName}") {
                            
                                //sh 'git config --global user.email "jenkins@examples.com"'
                                //sh 'git config --global user.name "Jenkinss"'
                                //sh "git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/jyauyo/${projectName}.git"
                                // Or use the git step directly:
                                // git branch: 'main', credentialsId: 'your-credential-id', url: 'https://github.com/your-org/your-repo.git'
        
                                // Crea una nueva rama y cambia a ella
                                //sh "git checkout -b ${newBranchName}"
            
                                // Simula la creación o modificación de archivos
                                // Reemplaza esto con las acciones que necesites para modificar tus archivos
                                sh "echo 'Contenido del nuevo archivo' > nuevo_archivo.txt"
            
                                // Agrega los cambios
                                //sh "git add ."
            
                                // Realiza el commit
                                //sh "git commit -m \"${commitMessage}\""

                           sh """
                           git config --global user.name "Ashfaque-9x"
                           git config --global user.email "ashfaque.s510@gmail.com" 
                           git checkout -b ${newBranchName}
                           git add .
                           git commit -m "Updated Deployment Manifest" -m "nroPase: ${nroPase}"
                            """
        
                                //sh "argocd login 192.168.184.131:443 --username jyauyo --password ad --insecure"
                            //withCredentials([usernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                            //withCredentials([gitUsernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", gitToolName: 'Default')]) {
                                // Envía la nueva rama al repositorio remoto
                                sh "git push https://github.com/jyauyo/${projectName}.git --set-upstream origin ${newBranchName}"
        
                                //sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${projectNameGit} HEAD:main"
                                echo "Rama '${newBranchName}' creada y cambios commiteados con éxito."
                            //}
                        }
                    }                    
                }
            }
        }
        
        stage('Build') {
            steps {
                sh "echo ************* Build ***************"
                sh "pwd"
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
                   //def projectName = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                   //echo("***** Project Name: ${projectName}");
                   
                   def docker_registry_environment_ = "${env.DOCKER_REGISTRY_ENVIRONMENT}"
                   def docker_registry_complete = "${env.DOCKER_REGISTRY}"
                   echo("***** Docker Registry Pre: ${docker_registry_environment_}");

                   //es un misterio
                   if (docker_registry_environment_ != null 
                       && !docker_registry_environment_.isEmpty() 
                       && !docker_registry_environment_.equals("null")) {
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

               writeFile file: 'nroPase.txt', text:"""${nroPase}"""
               
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
