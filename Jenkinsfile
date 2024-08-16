pipeline {
    agent any

    environment {
        MAVEN_ARGS = " -e clean install"
    }

    stages {
        stage('Clone Repository'){
            steps {
                bat 'git clone https://github.com/jumbolpc/spring-boot-kubernates.git'
            }
        }
        stage('Build Maven Project') {
            steps {
                withMaven(maven: 'MAVEN_HOME'){
                    bat "mvn ${MAVEN_ARGS}"
                }
            }
        }

        stage('Run Spring boot') {
            steps {
                bat "java -jar target/demo-0.0.1-SNAPSHOT.jar"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Build Success"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}