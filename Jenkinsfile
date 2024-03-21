pipeline {
    agent any
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
         maven 'M2_HOME'

    }

	//environment {	
	//	DOCKERHUB_CREDENTIALS=credentials('Docker')
	//} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                // git 'https://github.com/shushinz/Banking_Project.git'
		git branch: 'main', url: 'https://github.com/shushinz/Banking_Project.git'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
        stage("Docker build") {
            steps {
                sh 'docker version'
                sh "docker build -t dprasaddevops/bankapp-eta-app:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag dprasaddevops/bankapp-eta-app:${BUILD_NUMBER} dprasaddevops/bankapp-eta-app:latest"
            }       }
        stage('Login2DockerHub and Push the Image') {
    steps {
        script {
            // Retrieve Docker Hub credentials from Jenkins credentials
           // withCredentials([usernamePassword(credentialsId: 'docker-login-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
               withCredentials([usernameColonPassword(credentialsId: 'Docker', variable: 'Docker')]) {
    // some block
}
		echo "Docker Hub Username: $DOCKER_USERNAME"
                echo "Docker Hub Password: $DOCKER_PASSWORD"
                
                sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
		sh "docker push dprasaddevops/bankapp-eta-app:latest"
            }
        }
    }
}
		
		stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubeconfig(credentialsId: 'k8s', serverUrl: '') {
                        sh 'kubectl apply -f kubernetesdeploy.yaml'
                    } 
                }
            }
        }
    }
}
