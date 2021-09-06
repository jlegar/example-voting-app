pipeline {
	agent none

	stages {
		stage('WORKER - Build') {
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
				}
			}
			when {
				changeset '**/worker/**'
			}
			steps {
				echo 'Compiling worker app'
				dir('worker'){
					sh 'mvn compile'
				}
			}
		}

		stage('WORKER - Test'){
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
				}
			}
			when {
				changeset '**/worker/**'
			}
        	steps{
            	echo 'Running Unit Tets on worker app'
				dir('worker'){
					sh 'mvn clean test'
				}
          	}
      	}

		stage('WORKER - Package'){
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
				}
			}
			when {
				changeset '**/worker/**'
				branch 'master'
			}
        	steps{
				echo 'Creating archive artifacts'
				dir('worker'){
					sh 'mvn package -DskipTests'
					archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
				}
			}
		}

		stage('RESULT - Build'){
			agent {
				docker {
					image 'node:12-slim'
				}
			}
			when {
				changeset '**/result/**'
			}
			steps {
				echo 'Building NodeJS Result APP'
				dir('result'){
					sh 'npm install'
				}
			}
		}

		stage('RESULT - Test'){
			agent {
				docker {
					image 'node:12-slim'
				}
			}
			when {
				changeset '**/result/**'
			}
			steps {
				echo 'Unit Testing for NodeJS APP'
				dir('result'){
					sh 'npm install'
					sh 'npm run test'
				}
			}
		}

		stage('VOTE - Build'){
			agent {
				docker {
					image 'python:2.7.16-slim'
					args '--user root'
				}
			}
			when {
				changeset '**/vote/**'
			}
			steps {
				echo 'Building Vote Python APP'
				dir('vote'){
					sh 'pip install -r requirements.txt'
				}
			}
		}

		stage('VOTE - Test'){
			agent {
				docker {
					image 'python:2.7.16-slim'
					args '--user root'
				}
			}
			when {
				changeset '**/vote/**'
			}
			steps {
				echo 'Unit Testing for Python APP'
				dir('vote'){
					sh 'pip install -r requirements.txt'
					sh 'nosetests -v'
				}
			}
		}
		
		stage('Package'){
			agent any
			when {
				branch 'master'
				changeRequest()
			}
			steps {
				echo "Packaging WORKER with Docker Image Build"
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def vote_image = docker.build("jlegardevops/worker:v${env.BUILD_ID}", "./worker")
						vote_image.push()
						vote_image.push('latest')
					}
				}
				echo "Packaging RESULT with Docker Image Build"
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def vote_image = docker.build("jlegardevops/result:v${env.BUILD_ID}", "./result")
						vote_image.push()
						vote_image.push('latest')
					}
				}
				echo "Packaging VOTE with Docker Image Build"
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def vote_image = docker.build("jlegardevops/vote:v${env.BUILD_ID}", "./vote")
						vote_image.push()
						vote_image.push('latest')
					}
				}
			}
		}
	}
}
