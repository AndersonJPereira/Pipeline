pipeline{
	agent any
	stages{
		stage('Build Backend'){
			steps{
				dir('tasks-backend'){
				 	git 'https://github.com/AndersonJPereira/tasks-backend.git'
				 	bat 'mvn clean package -DskipTests=true'   
				}
			}
		}
		stage('Unit Tests'){
			steps{
				dir('tasks-backend'){
			    	bat 'mvn test'
				}
			}
		}
		stage('Container Sonnar-Docker'){
			steps{
				dir('src/main/resources/montagem/sonnar'){
			    	bat 'docker-compose up -d'
				}
			}
		}
	}
}


