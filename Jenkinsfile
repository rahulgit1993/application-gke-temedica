pipeline {
    agent any
    environment {
        PROJECT_ID = 'temedica-usa-npe-dev'
                CLUSTER_NAME = 'temedica-usa-npe-dev-gke'
                LOCATION = 'us-east1'
                CREDENTIALS_ID = 'kubernetes'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            steps {
                script {
                    app = docker.build("rahul1993sre/pipeline:${env.BUILD_ID}")
                    }
            }
        }
        
        stage('Push image') {
            steps {
                script {
                    withCredentials( \
                                 [string(credentialsId: 'dockerhub',\
                                 variable: 'dockerhub')]) {
                        sh "docker login -u rahul1993sre -p ${dockerhub}"
                    }
                    app.push("${env.BUILD_ID}")
                 }
                                 
            }
        }
    
        stage('Deploy to K8s') {
            steps{
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/pipeline:latest/pipeline:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', \
                  projectId: env.PROJECT_ID, \
                  clusterName: env.CLUSTER_NAME, \
                  location: env.LOCATION, \
                  manifestPattern: 'deployment.yaml', \
                  credentialsId: env.CREDENTIALS_ID, \
                  verifyDeployments: true])
                }
            }
        }    
}
