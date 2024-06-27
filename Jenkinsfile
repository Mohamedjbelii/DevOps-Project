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
            }
            post {
                always {
                    echo 'Hello from the Build stage!'
                }
            }
        }

        stage('Archive Artifact') {
            environment {
                WARPATH = '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/webapp/target/*.war'
                }
            steps {
                archiveArtifacts artifacts: $WARPATH, allowEmptyArchive: true
                sh 'mkdir -p /home/wars && cp $WARPATH /home/wars/'
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
                                        docker cp /usr/local/tomcat/webapps/*.war <container_id>:/usr/local/tomcat/webapps/
                                        docker exec 753ef1dae659 sh -c "catalina.sh stop"
                                        docker exec 753ef1dae659 sh -c "catalina.sh start"
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
                echo 'Notifying...'
            }
        }
    }
}
