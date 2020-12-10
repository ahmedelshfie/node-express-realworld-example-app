pipeline {

   environment {
        IMAGE_NAME = "docker.pkg.github.com/ahmedelshfie/expressCartNode/expressCart:${BUILD_NUMBER}"
     } 

    agent any

    stages {
	
	    stage ('Build-App') {
            steps {
               
                    sh ' npm run install'
					sh ' npm run test'
            }
        }
		
		
		stage ('Docker-Build-Login-Push-Image') {
            steps {

                withCredentials([[$class          : 'UsernamePasswordMultiBinding',
                                  credentialsId   : 'github_docker_id',
                                  usernameVariable: 'USERNAME',
                                  passwordVariable: 'PASSWORD']]) {
								  
					sh ' docker build . -t $IMAGE_NAME'			  
                    sh ' docker login docker.pkg.github.com -u $USERNAME -p $PASSWORD'
                    sh ' docker push $IMAGE_NAME '
					sh ' docker rmi $IMAGE_NAME '
                }
            }

        }  
		
		stage('Approval-deploy-kubernetes') {
            // no agent, so executors are not used up when waiting for approvals
            agent none
            steps {
                script {
                    def deploymentDelay = input id: 'Deploy', message: 'Deploy to production?', submitter: 'ahmed', parameters: [choice(choices: ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', '13', '14', '15', '16', '17', '18', '19', '20', '21', '22', '23', '24'], description: 'Hours to delay deployment?', name: 'deploymentDelay')]
                    sleep time: deploymentDelay.toInteger(), unit: 'HOURS'
                }
            }
        }	
		
		 stage ('Deploy-Kubernetes') {
            steps {
			
					sh ' kubectl set image deployment/polls-app polls-app=$IMAGE_NAME --namespace janna '
            }
        }  
		
		 stage ('Describe-Kubernetes-Deployment') {
            steps {
               
					sh ' kubectl describe deployment,svc -n janna'
            }
        } 
		
	}

}
