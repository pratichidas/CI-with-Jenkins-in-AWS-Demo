pipeline {
	agent any 	
	environment {
		PROJECT_ID = 'sincere-idea-275411'
		CLUSTER_NAME = 'k8cluster'
		LOCATION = 'europe-west1-c'
		CREDENTIALS_ID = 'demo-gcr'
	}
	stages {	
		stage('SCM Checkout') {            
			steps {
				checkout scm
			}	
		}
		stage('Build') { 
			steps {
				echo "Cleaning and packaging..."
				sh 'mvn clean package'		
			}
		}
		stage('Test') { 
			steps {
				echo "Testing..."
				sh 'mvn test'
			}
		}
		stage('Build Docker Image') { 
			steps {
				script {
					myapp = docker.build("gcr.io/sincere-idea-275411/pratichidas/demogcrapp:${env.BUILD_ID}")
				}
			}
		}
		stage("Push Docker Image") {
			steps {
				script {
					docker.withRegistry('https://gcr.io') {
						myapp.push("${env.BUILD_ID}")		
					}
				}
			}
		}
		stage('Deploy to K8s') { 
			steps {
				echo "Deployment started ..."
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished ..."
			}
		}
	}
}
