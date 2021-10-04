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
		stage('Docker SonnarQube'){
			steps{
				bat 'docker-compose -f docker-compose-sonnar.yml up -d'
			}
		}
		stage('SonarQube-Analysis'){
			environment{
			    scannerHome=tool 'SONAR_SCANNER'
			}
			steps{
				withSonarQubeEnv('SONAR_LOCAL'){
					sleep(30)
					bat	"${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackEnd-Analysis -Dsonar.host.url=http://localhost:9000 -Dsonar.login=caafd132ad677a291f5c763e1421bfb25f492905 -Dsonar.java.binaries=tasks-backend/target -Dsonar.coverage.exclusions=**/.mvn/**,**/model**,**/src/test/**,**Application.java"  
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
		stage('Docker Deploy Backend'){
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
		stage('Docker Deploy Frontend'){
			steps{
				bat 'docker-compose -f docker-compose-deployfrontend.yml up -d'
			}
		}
		stage('Docker Ambiente Teste'){
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
		stage('Deploy Prod'){
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
	}
}


