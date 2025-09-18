@Library("my-shared-library@develop")
//import sharedlib.DockerJenkinsUtils
import sharedlib.GitOpsJenkinsUtils
//def utilsDocker = new DockerJenkinUtils(this)
def utilsGitOps = new GitOpsJenkinsUtils(this)

//def projectName
//def nroPase
//def projectNameGit

pipeline {
    //agent none
    agent {
        docker {
            image 'jyauyor/maven-argocd-jdk21:1.0.3'
            args '-v /var/run/docker.sock:/var/run/docker.sock -v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS=-Duser.home=/var/maven'
        }
    }

    options {
        skipDefaultCheckout()
    }
    environment {
        //JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        //DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        DOCKER_REGISTRY = "jyauyor"
        DOCKER_URL = "https://index.docker.io/v1/"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        GITHUB_CREDENTIALS_ID = "github-credentials-jyauyo"
        ARGOCD_CREDENTIALS_ID = "argocd-credentials"
        DOCKER_REGISTRY_ENVIRONMENT = ""
        DOCKER_CONFIG = "/tmp/.docker"
    }
   
    //tools {
    //    maven 'Maven Apache'
    //}

    parameters {
        string(name: 'BRANCH', defaultValue: 'develop', description: '')
    }
    
    stages {
        stage('Prepare') {

            //agent {
                //docker {
                    //image 'maven:3.9.11-eclipse-temurin-21'
                    //args '-v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS=-Duser.home=/var/maven'
                //}
            //}

            steps {
                
                checkout scm                
                
                script {
                    sh 'mvn --version'
                    sh 'java --version'
                    //sh 'argocd version'
                    
                    utilsGitOps.prepare()
                    /*projectName = scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
                    echo("***** Project Name: ${projectName}");
                    
                    def pom = readMavenPom file: 'pom.xml'
                    nroPase = pom.properties.nroPase
                    echo "***** NroPase: ${nroPase}"
                    
                    APP_VERSION = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                    echo "***** Version: ${APP_VERSION}"*/
                    
                }
                
            }
        }

        stage('Compile') {
            //when {
            //    expression { false }
            //}
            //agent {
                //docker {
                    //image 'maven:3.9.11-eclipse-temurin-21'
                    //args '-v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS=-Duser.home=/var/maven'
                //}
            //}            
            steps {                
                sh "pwd"
                // Compilar el proyecto usando Maven
                sh 'mvn clean compile'
                sh 'mvn package'
            }
        }

        stage('Build & Push Image') {
            //when {
            //    expression { false }
            //}
            //agent {
                //docker {
                    //image 'docker:28.1.1'
                    //args '-v /var/run/docker.sock:/var/run/docker.sock'
                //}
            //}
            steps {
                script {
                    utilsGitOps.buildAndPushImage()
                }
               //echo "***** Cleaning ..."
               //sh 'mvn clean'

               //writeFile file: 'nroPase.txt', text:"""${nroPase}"""               
            }
        }

        stage('Clonacion Update YAML') {
            when {
                expression { false }
            }
            steps {
                script {
                    //projectNameGit = scm.getUserRemoteConfigs()[0].getUrl()
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

        stage('Sync with ArgoCD') {
            //when {
            //    expression { false }
            //}
            //agent {
                //docker {
                    //image 'argoproj/argocd:v2.6.15'
                    //args '-u root'
                //}
            //}
            steps {                
                script {
                    def argocdRepoYaml = "jyauyo/gitops-argocd.git"
                    def argocdNamespace = "demo"
                    def argocdProject = "demo"
                    sh 'pwd'
                    sh 'mkdir yuyin'
                    dir("yuyin") {
                        sh 'pwd'
                        utilsGitOps.syncWithArgoCd(argocdRepoYaml, argocdNamespace, argocdProject)                        
                    }
                    
                }
            }
        }

    }     
    
    /*post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed.'
        }
    }*/
}
