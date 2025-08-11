pipeline {
    
    agent { label 'ansible' }
    
    options {
      buildDiscarder logRotator(numToKeepStr: '5')
      timeout(time: 10, unit: 'MINUTES')
      disableConcurrentBuilds()
    }
	
	environment {
		TOMCAT_SERVER_IP = "172.31.31.48"
	}
	
	tools {
		maven 'Maven-3.9.10'
	}
	
	stages {
	
		stage('Git Clone') {
            steps {
                git branch: 'development', credentialsId: 'GitHubCred', url: 'https://github.com/Supriya-lr/student-reg-webapp.git'
            }
        }
	
		stage('Maven Build Package'){
			steps{
				sh "mvn clean package"
			}
		}
		
		stage('Deploy to Tomcat') {
			steps{
				sshagent(['New-Tomcat-Server']) {
					sh """
					    ssh -o StrictHostKeyChecking=no ec2-user@${TOMCAT_SERVER_IP} sudo systemctl stop tomcat 
                        echo Stopping the Tomcat Process on ${TOMCAT_SERVER_IP}
						sleep 30
						scp -o StrictHostKeyChecking=no target/student-reg-webapp.war ec2-user@${TOMCAT_SERVER_IP}:/opt/tomcat/webapps/student-reg-webapp.war
                        echo Copying the War file to Tomcat Server ${TOMCAT_SERVER_IP}
						ssh -o StrictHostKeyChecking=no ec2-user@${TOMCAT_SERVER_IP} sudo systemctl start tomcat
                        echo Starting the Tomcat Process on ${TOMCAT_SERVER_IP}
                    """ 
				}    
			}
		}
	}
}

