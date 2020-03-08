pipeline {
    agent any
    environment {
        PROJECT_ID = 'devops-learning-265821'
        CLUSTER_NAME = 'k8s-cluster'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'Kubernetes'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
		 stage("Build") {
            steps {
               echo "cleaning and packaging"
			   sh 'mvn clean package'
            }
        }
		 stage("Test") {
            steps {
                echo "Testing"
			   sh 'mvn test'
            }
        }
        stage("Build image") {
            steps {
                script {
                    //myapp = docker.build("naugraiyashubham/k8s:${env.BUILD_ID}")
			myapp=docker.build("gcr.io/devops-learning-265821/naugraiyashubham/k8s:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    //docker.withRegistry('https://registry.hub.docker.com', 'Docker-hub-credentials') {
                           // myapp.push("${env.BUILD_ID}")
			docker.withRegistry('https://gcr.io', 'gcr:GCR_Credential') {
				myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
			    echo "Deployment started"
				sh 'ls -ltr'
				sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' Deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'Deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished"
            }
        }
    }    
}
