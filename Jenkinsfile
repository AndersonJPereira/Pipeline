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
		stage('Deploy Backend'){
			steps{
				deploy adapters: [tomcat8(credentialsId: 'TomCatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
			}
		}
		stage('API Tests'){
			steps{
				dir('api-tests'){
					git 'https://github.com/AndersonJPereira/tasks-api-test.git'
					bat 'mvn clean test'			    
				}
			}
		}
	}
}


