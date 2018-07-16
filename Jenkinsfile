pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'localhost', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'localhost', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /etc/ansible/jenkins/jenkins.pem **/target/*.war root@${params.tomcat_dev}:/media/jenkins/tomcat/apache-tomcat-8.5.32-staging/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /etc/ansible/jenkins/jenkins.pem **/target/*.war root@${params.tomcat_prod}:/media/jenkins/tomcat/apache-tomcat-8.5.32-prod/webapps"
                    }
                }
            }
        }
    }
}
