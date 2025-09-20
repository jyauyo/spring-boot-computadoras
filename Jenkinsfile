@Library("my-shared-library@develop") _
import sharedlib.GitOpsJenkinsUtils

def utilsGitOps = new GitOpsJenkinsUtils(this)

def argocdRepoYaml = "jyauyo/gitops-argocd.git"

pipeline {
    //agent defaultAgent()
    agent any
    //agent {
        //docker {
            //image 'jyauyor/maven-argocd-jdk21:1.0.4'
            //args '-v /var/run/docker.sock:/var/run/docker.sock -v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS=-Duser.home=/var/maven'
        //}
    //}
    
    options {
        skipDefaultCheckout()
    }

    environment {
        PUB_KEY = credentials('secret-github')
        //JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
        //DOCKER_REGISTRY = credentials('docker-registry') //"your_dockerhub_username/your_repository"
        //DOCKER_REGISTRY = "jyauyor"
        //DOCKER_URL = "https://index.docker.io/v1/"
        //DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        //GITHUB_CREDENTIALS_ID = "github-credentials-jyauyo"
        //ARGOCD_CREDENTIALS_ID = "argocd-credentials"
        //DOCKER_REGISTRY_ENVIRONMENT = ""
        //DOCKER_CONFIG = "/tmp/.docker"
        //ARGOCD_CONFIG_DIR = "/tmp/.config/argocd/config"
    }
   
    //tools {
    //    maven 'Maven Apache'
    //}

    parameters {
        string(name: 'BRANCH', defaultValue: 'develop', description: '')
    }
    
    stages {
        stage('Prepare') {
            agent {
                docker {
                    image 'jyauyor/maven-argocd-jdk21:1.0.4'
                    args '-v /var/run/docker.sock:/var/run/docker.sock -v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS=-Duser.home=/var/maven'
                }
            }
            steps {
                checkout scm
                script {
                    sh 'mvn --version'
                    sh 'java --version'

                    utilsGitOps.prepare()
                }
            }
        }

        stage('Compile') {
            when {
                expression { false }
            }
            steps {                
                sh 'mvn clean compile'
                sh 'mvn package'
                //sh 'mvn clean package'
            }
        }

        stage('Build & Push Image') {
            when {
                expression { false }
            }
            steps {
                script {
                    utilsGitOps.buildAndPushImage()
                }
               sh 'mvn clean'
            }
        }

        stage('Update YAML') {
            //when {
            //    expression { false }
            //}
            
            //agent {
                //docker {
                    //image 'jyauyor/maven-argocd-jdk21:1.0.4'
                    //args '-v /var/run/docker.sock:/var/run/docker.sock'
                //}
            //}
            steps {
                script {
                    //projectNameGit = scm.getUserRemoteConfigs()[0].getUrl()
                    /*sh """ 
                    #!/bin/bash                    
                    pwd
                    cd ..
                    rm -rf ${env.NRO_PASE}
                    mkdir ${env.NRO_PASE}
                    pwd
                    """
                    */
                    def newBranchName = "feature/${env.NRO_PASE}"
                    def commitMessage = "Agrega la funcionalidad XYZ en la rama" 
                    
                    //sh 'git config --global --local user.email "jenkins@example.com"'
                    //sh 'git config --global --local user.name "Jenkins"'
                    //sh 'ssh -T git@github.com'
                    dir("${env.NRO_PASE}") {
                        //withCredentials([usernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                            
                            //sh "git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/jyauyo/${env.PROJECT_NAME}.git"
                        
                            sh "git remote set-url origin git@github.com:jyauyo/${env.PROJECT_NAME}.git"
                            sh "ssh -vT git@github.com"
                        
                            dir("${env.PROJECT_NAME}") {
                                sh "git checkout ${BRANCH}"
                                sh "git pull"
                                echo "rama clonada ${BRANCH}"

                                sh "git checkout -b ${newBranchName}"
            
                                // Simula la creación o modificación de archivos
                                // Reemplaza esto con las acciones que necesites para modificar tus archivos
                                sh "echo 'Contenido del nuevo archivo' > nuevo_archivo.txt"
            
                                // Agrega los cambios
                                sh "git add ."
            
                                // Realiza el commit
                                sh "git commit -m \"${commitMessage}\""

                                sh "git push --set-upstream origin ${newBranchName}"
                                
                            }
                        //}
                    }
                    
                    
                    sh "pwd"
                    //sh "cd .."
                    //sh "mkdir clonacion"
                    //sh "cd clonacion"                   
                    //dir("${env.NRO_PASE}") {
                        //dir("${PROJECT_NAME}") {
                            
                                //sh 'git config --global user.email "jenkins@examples.com"'
                                //sh 'git config --global user.name "Jenkinss"'
                                //sh "git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/jyauyo/${projectName}.git"
                                // Or use the git step directly:
                                // git branch: 'main', credentialsId: 'your-credential-id', url: 'https://github.com/your-org/your-repo.git'
        
                                // Crea una nueva rama y cambia a ella
                                //sh "git checkout -b ${newBranchName}"
            
                                // Simula la creación o modificación de archivos
                                // Reemplaza esto con las acciones que necesites para modificar tus archivos
                                //sh "echo 'Contenido del nuevo archivo' > nuevo_archivo.txt"
            
                                // Agrega los cambios
                                //sh "git add ."
            
                                // Realiza el commit
                                //sh "git commit -m \"${commitMessage}\""

                           //sh """
                           //git config --global user.name "Ashfaque-9x"
                           //git config --global user.email "ashfaque.s510@gmail.com" 
                           //git checkout -b ${newBranchName}
                          // git add .
                           //git commit -m "Updated Deployment Manifest" -m "nroPase: ${nroPase}"
                           // """        
                                
                            //withCredentials([usernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                            //withCredentials([gitUsernamePassword(credentialsId: "${env.GITHUB_CREDENTIALS_ID}", gitToolName: 'Default')]) {
                                // Envía la nueva rama al repositorio remoto
                                //sh "git push https://github.com/jyauyo/${projectName}.git --set-upstream origin ${newBranchName}"
        
                                //sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${projectNameGit} HEAD:main"
                                //echo "Rama '${newBranchName}' creada y cambios commiteados con éxito."
                            //}
                        //}
                }
            }
        }

        stage('Sync with ArgoCD') {
            when {
                expression { false }
            }
            steps {                
                script {
                    
                    def argocdNamespace = "demo"
                    def argocdProject = "demo"

                    utilsGitOps.syncWithArgoCd(argocdRepoYaml, argocdNamespace, argocdProject)                        
                    
                }
            }
        }
    }     

}
