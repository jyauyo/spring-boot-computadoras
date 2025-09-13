pipeline {
    agent any
    //environment {
    //    JAVA_TOOL_OPTIONS = "-Duser.home=/home/jenkins"
    //}
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
        string(name: 'BRANCH', defaultValue: 'algo', description: '')
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Clonar el repositorio desde GitHub
                git url: 'https://github.com/jyauyo/spring-boot-computadoras.git', branch: "${params.BRANCH}"
            }
        }
        stage('Build') {
            steps {
                // Compilar el proyecto usando Maven
                sh 'mvn clean install'
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
