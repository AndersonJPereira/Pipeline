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
	}
}


