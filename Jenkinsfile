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
				dir('src/main/resources/montagem/sonnar'){
			    	bat 'docker-compose up -d'
				}
			}
		}
		stage('SonarQube-Analysis'){
			environment{
			    scannerHome=tool 'SONAR_SCANNER'
			}
			steps{
				withSonarQubeEnv('SONAR_LOCAL'){
				    sleep(30)
					bat	"${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackEnd-Analysis -Dsonar.host.url=http://localhost:9000 -Dsonar.login=2d216e5a1375a2b65e3c12ab5e89ac764fd57783 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/model**,**/src/test/**,**Application.java"  
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
	}
}


