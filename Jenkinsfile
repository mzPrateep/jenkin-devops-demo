
pipeline{

	 environment {
    	registry = "mzprateep/eks-jenkin-demo"
    	registryCredential = 'docker-prateep'
    	dockerImage = ''
		region = "ap-southeast-1"
		clusterName  = "prateep-labs"
  	}

	agent any
	
	stages {


		stage('Build') {
			steps {
			script {
         		 dockerImage = docker.build registry + ":$BUILD_NUMBER"
       		 }
			}
		}
  

		stage('Push image') {
			steps {
				 script {
            		docker.withRegistry( '', registryCredential ) {
           			dockerImage.push()
         		 }
			}
		}

		}
        
		stage('Remove Unused docker image') {
     		 steps{
        		sh "docker rmi $registry:$BUILD_NUMBER"
     	 	}
   		}

		stage('aws creadentials'){
			  steps {
			  
			//   withCredentials([[
   			// 		$class: 'AmazonWebServicesCredentialsBinding',
   			// 		credentialsId: "eks-credentials",
   			// 		accessKeyVariable: 'AWS_ACCESS_KEY_ID',
   			// 		secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
			// 	]]) {
   			// 		 // AWS Code
			// 	}
			  withAWS(credentials: 'aws-prateep', region: 'ap-southeast-1') {

				  sh "aws iam list-account-aliases"
				  sh "aws eks --region $region update-kubeconfig --name $clusterName"

				//   sh "cp /var/lib/jenkins/.kube/config  /home/ubuntu/.kube/config"

				  sh 'kubectl get pods'
				   sh 'kubectl get nodes'
				  
			  }

			  }

		}

        stage('eks deploy') {

			steps {
						  withAWS(credentials: 'aws-prateep', region: 'ap-southeast-1') {
                            				  sh "aws eks --region $region update-kubeconfig --name $clusterName"
                            				  sh 'kubectl get pods'
                            				  sh 'kubectl get nodes'
                            				  sh "kubectl apply -f eks-example-deployment.yaml"

            			  }
				sh 'echo Hello World'
				sh 'kubectl get pods'
//                 sh "sed -i 's/hellonodejs:latest/hellonodejs:eks/g' deploy.yaml"
//                 sh 'kubectl apply -f eks-deployment.yaml'
                // sh 'kubectl rollout restart deployment hello-world-nodejs'
//                 			  sh "aws iam list-account-aliases"

			}
		}
	}


}

