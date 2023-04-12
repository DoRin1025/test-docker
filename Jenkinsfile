pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerRegistry')
    }
    stages {
	
	    stage('Build Docker image') {
		    when {
                branch 'dev'
            }
            steps {
                 sh 'docker image build -t test-site .'
            }
        }
		
        stage('Login') {
            steps {
                 sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login registry.ismartapps.com.au:5000 -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
		
        stage('Publish Docker image') {
            when {
                branch 'dev'
            }		
            steps {
	       sh 'docker tag test-site registry.ismartapps.com.au:5000/test-site'
               sh 'docker push registry.ismartapps.com.au:5000/test-site'
            }
        
            
        }
    }
}
