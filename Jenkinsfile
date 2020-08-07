pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    
    stages{
        stage('Initialize'){
            steps{
                sh ''' 
                        echo "PATH = ${PATH}"
                        echo "M2_HOME = ${M2_HOME}"
                    '''             
            }
        }
        stage('Check-Git-Secrets'){
            steps{
                sh 'rm trufflehog || true'
                sh 'docker run gesellix/trufflehog --json https://github.com/arch4sec/DevSecOps.git > trufflehog'
                sh 'cat trufflehog'
            }
        }
       stage ('SAST'){
           steps{
            withSonarQubeEnv('sonar') {
			sh 'mvn sonar:sonar'
			sh 'cat target/sonar/report-task.txt'
		       }
            }
        }

        stage ('Build'){
            steps{
            sh 'mvn clean package'
            }
        }
        stage ('Deploy-toTomcat'){
            steps{
                sshagent(['tomcat']){
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@18.196.102.146:/home/ec2-user/apache-tomcat-8.5.57/webapps/webapp.war'
                }
                
            }
        }
    }
}
