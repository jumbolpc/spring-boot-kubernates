pipeline {
    agent any

    environment {
        MAVEN_ARGS = " -e clean install"
        DOCKER_IMAGE = "jumbolpc/springboot-app-jenkins:latest"
        KUBE_CONFIG_PATH = 'C:\\Users\\jumbo\\.kube\\config' // linux, mac /User/jumbo/.kube/config
        KUBE_NAMESPACE = 'default'
    }

    stages {
        // stage('Clone Repository'){
        //     steps {
        //         bat 'git clone https://github.com/jumbolpc/spring-boot-kubernates.git'
        //     }
        // }
        stage('Build Maven Project') {
            steps {
                withMaven(maven: 'MAVEN_HOME'){
                    bat "mvn ${MAVEN_ARGS}"
                }
            }
        }

        // stage('Run Spring boot') {
        //     steps {
        //         bat "java -jar target/demo-0.0.1-SNAPSHOT.jar"
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE} ."

                bat "docker image prune -f"

                bat "docker images"
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-credentials', variable: 'DOCKERHUB_PASSWORD')]) {
                    bat "docker login -u jumbolpc -p %DOCKERHUB_PASSWORD%"
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy to Kubernates') {
            steps {
                bat "kubectl --kubeconfig=${KUBE_CONFIG_PATH} apply -f deployment.yaml -n ${KUBE_NAMESPACE}"
                bat "kubectl --kubeconfig=${KUBE_CONFIG_PATH} rollout status deployment/springboot-app -n ${KUBE_NAMESPACE}"
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