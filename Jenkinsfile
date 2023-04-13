pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerRegistry')
	DOCKER_REGISTRY = 'registry.ismartapps.com.au:5000'
	DOCKER_IMAGE = 'test-site'
    }
    stages {
	
	    stage('Build Docker image') {
		    when {
                branch 'dev'
            }
            steps {
                 sh 'docker image build -t $DOCKER_IMAGE .'
            }
        }
		
        stage('Login') {
            steps {
                 sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login $DOCKER_REGISTRY -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
		
        stage('Publish Docker image') {
            when {
                branch 'dev'
            }		
            steps {
	       sh 'docker tag $DOCKER_IMAGE $DOCKER_REGISTRY/$DOCKER_IMAGE'
               sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE'
            }
        
            
        }
        stage('Deploy on Instance 6') {
            when {
                branch 'dev'
            }
            steps {
                script{
                    command='''
                        pwd
			docker rmi $(docker images | grep registry.ismartapps.com.au:5000/test-site | grep "<none>" |  awk '{print $3}') --force
			docker pull registry.ismartapps.com.au:5000/test-site:latest
			docker stop test-site
			docker rm test-site
			docker run --name test-site -d -p 825:80 registry.ismartapps.com.au:5000/test-site:latest
                    '''
                  // Execute commands
                  sshPublisher(publishers: [
				         sshPublisherDesc(
						 configName: 'Instance-6',
						 verbose: true,
                         transfers: [ sshTransfer(execCommand: command    )])])
						 
				   flag = true
                     
                }
            }
        }		
    }
}
