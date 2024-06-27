pipeline {
    agent any
    environment {
        WARPATH = '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/webapp/target/*.war'
        WARDIR = "${WORKSPACE}/wars"
    }
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
                sh 'mvn clean install -Dmaven.test.skip=true'
            }
            post {
                always {
                    echo 'Hello from the Build stage!'
                }
            }
        }

        stage('Archive Artifact') {

            steps {
                archiveArtifacts artifacts: WARPATH, allowEmptyArchive: true
                sh 'mkdir -p ${WARDIR} && cp ${WARPATH} ${WARDIR}/'
            }
            post {
                success {
                    echo 'Hello from the archiveArtifacts stage!'
                }
            }
        }

        stage('Deploy to Tomcat') {

            steps {
                script {

                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "dokerhost",
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'WARPATH',
                                        removePrefix: '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/webapp/target/',
                                        remoteDirectory: '/usr/local/tomcat/webapps',
                                        execCommand: '''
                                            docker cp /usr/local/tomcat/webapps/*.war 753ef1dae659:/usr/local/tomcat/webapps/
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
        }

        stage('Notification') {
            steps {
                echo 'Notifying...'
            }
        }
    }
}
