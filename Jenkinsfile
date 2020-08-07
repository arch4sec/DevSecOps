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
                sh 'docker run gesellix/trufflehog --json https://github.com/arch4sec/webapp.git > trufflehog'
                sh 'cat trufflehog'
            }
        }
        stage('Source dependency check'){
            steps{
                sh 'rm owasp-dependency-check.sh || true'
                sh 'wget "https://raw.githubusercontent.com/arch4sec/webapp/master/owasp-dependency-check.sh" '
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
                sh 'cat /var/lib/jenkins'
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
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.250.146.239:/home/ubuntu/prod/apache-tomcat-8.5.57/webapps/webapp.war'
                }
                
            }
        }
    }
}
