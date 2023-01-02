pipeline {
    agent {label 'agentlinux'} //using ubuntu18 jenkins agent
    
    environment {
	  registry = "sahmed448/my-smart-app"
	  img = "$registry" + ":${env.BUILD_ID}"
	  registryCredential = 'docker-hub-login' //withRegistry needs just credentialsId so give like this
    }	
    
    stages {
	    
	    
        stage('Scm checkout') 
			{
             steps {
				echo "Checkout"
                //Eventually we should comment below line, since git will implicitly do the checkout since we have selected scm option in our jenkins job
				git branch: 'main', credentialsId: 'jenkins-github-token', url: 'https://github.com/sahmed448/SmartFlaskAPP.git' // using git hub token to authenticate
				sh 'ls -la'
                }
			}
			
        stage('Unit Tests') 
			{
            steps {
				echo "Unit Tests"
			    sh('bash ./jenkinsscript.sh')
			}
			post {//Post section within stage
			     success{
					echo "Post Stage-Publish Junit"
					junit skipMarkingBuildUnstable: true, testResults: 'xmlReport/output.xml'
					}
				 }
			}
        
        stage('Build Image Locally') 
			{
            steps {
				echo "Building our image"
				script {
				    println{"${img}"}
					dockerImg = docker.build("${img}")
					}
				}
			}
			
		stage('Push to Docker Hub') 
			{
            steps {
				script 
					{
					docker.withRegistry('http://registry.hub.docker.com', registryCredential) {
						dockerImg.push()
						//dockerImg.push('latest') //One more push for latest tag
						}				
					}
				}
			}
		
		stage('Deploy Run') 
		{
			steps {
				script {
				    sh returnStatus:true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
					cont = docker.image("${img}").run('--rm -d --name ${JOB_NAME} -p 5000:5000')
					}
				}
		    }	

	    
    }
    
       post {
		always {
			echo "In POST stage"
			script {
			    
				if ((currentBuild.currentResult != "FAILURE") && cont) {
					sh returnStatus:true, script: ('docker rmi $(docker images --filter "before=${img}" | grep ${registry} | awk \'{print $3}\') --force')
				} else if (currentBuild.previousSuccessfulBuild) {
					prevImg = "$registry" + ":${currentBuild.previousSuccessfulBuild.number}"
					sh("docker run --rm -d --name ${JOB_NAME} -p 5000:5000 ${prevImg}")
				}

			}
		}

	}

}