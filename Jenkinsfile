pipeline {
    agent any
    tools {
            maven 'maven-3.9.8'
            jdk 'java-13'
        }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat "mvn clean install -Dmaven.test.skip=true"
            }  post {
                                  always {
                                      echo 'Hello from the Build stage!'
                                  }
                              }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'webapp/target/*.war', allowEmptyArchive: true
                sh 'mkdir -p /home/wars && cp webapp/target/*.war /home/wars/'
            }
            post {
                success {
                    echo 'Hello from the archiveArtifacts stage!'
                }
            }
        }

       stage('Deploy to Tomcat') {
                   steps {
                       sshPublisher(
                           publishers: [
                               sshPublisherDesc(
                                   configName: "tomcat host ssh",
                                   transfers: [
                                       sshTransfer(
                                           sourceFiles: '/home/wars/*.war',
                                           removePrefix: '/home/wars',
                                           remoteDirectory: '/usr/local/tomcat/webapps',
                                           execCommand: '''
                                               docker exec <container_id> sh -c "catalina.sh stop"
                                               docker exec <container_id> sh -c "catalina.sh start"
                                           '''
                                       )
                                   ],
                                   usePromotionTimestamp: false,
                                   useWorkspaceInPromotion: false,
                                   verbose: true
                               )
                           ]
                       )
                   }
               }
        stage('Notification') {
            steps {
            	                echo 'Notifiying...'
            }
        }
    }
}
