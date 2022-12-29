pipeline {
    agent any
    stages {
	    
	    
        stage('Checkout Code') 
	    {
             steps {
				echo "Checkout"
                //commenting below line, since git will implicitly do the checkout since we have selected scm option in our jenkins job
				//git branch: 'main', url: 'https://github.com/sahmed448/SmartFlaskAPP.git'
				sh 'ls -l'
                }
        }


         stage('Unit Tests') 
	      {
            steps {
				echo "Unit Tests"
				//sh(returnStatus: true, script: '. ~/.bashrc \n pyenv version')
				sh('chmod +x ./jenkinsscript.sh')
				//('bash ./test.sh')
			}
        }
	    
		stage("Publish Junit report") 
		{
            steps{
				echo "Publish Junit"
				junit skipMarkingBuildUnstable: true, testResults: 'xmlReport/output.xml'
            }
        }
	    
		stage ("Publish Code Coverage") 
		{
            steps{
				echo "Publish code coverage"
				cobertura coberturaReportFile: 'coverage.xml'
            }
        }
	    
	    
    }
}