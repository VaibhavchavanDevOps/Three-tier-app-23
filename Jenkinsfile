pipeline {
    agent any

	triggers {
        githubPush() // Trigger the pipeline when a push event occurs
    }
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker')
        //FRONTEND_IMAGE = 'vaibhavnitor/frontend-image'
        BACKEND_IMAGE = 'vaibhavnitor/backend-22'
        //DATABASE_IMAGE = 'vaibhavnitor/database-image'
        // GKE Cluster Details
        GKE_CLUSTER_NAME = 'my-first-cluster-1'
        GKE_ZONE = 'us-central1-c'
        GKE_PROJECT = 'my-project-dotnet-445205'
		

    }

    stages {
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/VaibhavchavanDevOps/Three-tier-app-23.git'
            }
		}

        //stage('Build Frontend Docker Image') {
          //  steps {
            //    script {
                    //Replace 'frontend' with the path to the frontend Dockerfile
              //      sh 'docker  build -t ${FRONTEND_IMAGE}:latest  ElectronicEquipmentAngular'
                //}
            //}
        //}
        //stage('Build Database Docker Image') {
          //steps {
            //  script {
                    //Replace 'database' with the path to the database Dockerfile
              //   sh 'docker build -t ${DATABASE_IMAGE}:latest .'
                //  sh 'docker run -d -p 1433:1433 ${DATABASE_IMAGE}:latest'
                //}
            //}
	    //}

        stage('Build Backend Docker Image') {
            steps {
                script {
                    // Replace 'backend' with the path to the backend Dockerfile
                    sh 'docker  build -t ${BACKEND_IMAGE}:latest  ElectricEquipmentDotNetCoreAPI'
                }
            }
        }
		//stage("TRIVY"){
          //  steps{
            //    sh "trivy image ${FRONTEND_IMAGE}:latest "
			//	sh "trivy image ${BACKEND_IMAGE}:latest "
			//	sh "trivy image ${DATABASE_IMAGE}:latest "
            //}
        //}
        //stage('deploy to container'){
          //  steps{
            //    script{
                        //sh'docker run -d -p 80:80 ${FRONTEND_IMAGE}:latest'
                        //sh'docker run -d -p 81:80 ${BACKEND_IMAGE}:latest'
              //  }
            //}
        //}
        stage('Push Images to Docker Hub') {
            steps {
              script {
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
 
                // Push the Docker images
                //h 'docker push ${FRONTEND_IMAGE}:latest'
                sh 'docker push ${BACKEND_IMAGE}:latest'
                //sh 'docker push ${DATABASE_IMAGE}:latest'
            }
        }
		}
        }
        stage('Deploy to GKE') {
            steps {
                script {
                    // Authenticate with GKE
                    withCredentials([file(credentialsId: 'gke-credentials', variable: 'GKE_CREDENTIALS')]) {
                        // Authenticate with GCloud
                        sh '''
                            gcloud auth activate-service-account --key-file=${GKE_CREDENTIALS}
                            gcloud config set project ${GKE_PROJECT}
                            gcloud container clusters get-credentials ${GKE_CLUSTER_NAME} --zone ${GKE_ZONE}
                        '''
                        
                        // Deploy to GKE
                        sh '''
                            kubectl apply -f manifest/Secret.yaml
                            kubectl apply -f manifest/Backend-deployment.yaml
                            kubectl apply -f manifest/backend-service.yaml
                            kubectl apply -f manifest/NetworkPolicy.yaml
                            
                        '''
                        
                        // Verify deployments
                        sh '''
                            kubectl get deployments
                            kubectl get services
                            kubectl get pods
                        '''
						}
					}
				}
			}
  }
}
