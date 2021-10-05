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
		stage('Docker SonarQube Up'){
			steps{
				bat 'docker-compose -f docker-compose-sonnar.yml up -d'
				sleep(90)
			}
		}
		stage('SonarQube-Analysis'){
			environment{
			    scannerHome=tool 'SONAR_SCANNER'
			}
			steps{
				withSonarQubeEnv('SONAR_LOCAL'){
					bat	"${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackEnd-Analysis -Dsonar.host.url=http://localhost:9000 -Dsonar.login=a4ce6f97d657557a96f9145c9536ce77495a35e0 -Dsonar.java.binaries=tasks-backend/target -Dsonar.coverage.exclusions=**/.mvn/**,**/model**,**/src/test/**,**Application.java"  
				}
			}
		}
		stage('Quality Gate'){
			steps{
				sleep(10)
				timeout(time:1, unit:'MINUTES'){
					waitForQualityGate abortPipeline:true 
				}
			}
		}
		stage('Docker SonarQube Down'){
			steps{
				bat 'docker stop sonar pg-sonar'
			}
		}
		stage('Docker Deploy Backend Up'){
			steps{
				bat 'docker-compose -f docker-compose-deploybackend.yml up -d'
			}
		}
		stage('API Tests'){
			steps{
				dir('api-tests'){
					sleep(30)
					git 'https://github.com/AndersonJPereira/tasks-api-test.git'
					bat 'mvn clean test'			    
				}
			}
		}
		stage('Docker Deploy Frontend Up'){
			steps{
				bat 'docker-compose -f docker-compose-deployfrontend.yml up -d'
			}
		}
		stage('Docker Seleniun Grid Up'){
			steps{
				bat 'docker-compose -f docker-compose-ambienteTST.yml up -d'
			}
		}
		stage('Functional Tests'){
			steps{
				dir('functional-tests'){
					sleep(30)
					git 'https://github.com/AndersonJPereira/tasks-functional-test.git'
					bat 'mvn clean test'			    
				}
			}
		}
		stage('Docker Deploy Frontend Backend Down'){
			steps{
				bat 'docker stop frontend-tst backend-tst pg-tasks'
			}
		}
		stage('Docker Deploy Prod Up'){
			steps{
				bat 'docker-compose -f docker-compose-ambientePRD.yml up -d'			
			}
		}
		
		stage('Health Check'){
			steps{
				dir('functional-tests'){
					sleep(30)
					bat 'mvn verify -Dskip.surefire.tests'
				}			
			}
		}
		stage('Docker Seleniun Grid Down'){
			steps{
				bat 'docker stop Chrome-1 Chrome-2 selenium-hub'		
			}
		}
		stage('Docker Deploy Prod Down'){
			steps{
				bat 'docker stop backend-prod frontend-prod pg-prod'		
			}
		}
	}
}


