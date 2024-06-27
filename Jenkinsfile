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
                sh 'mvn clean install -Dmaven.test.skip=true'
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
                WARDIR = "${WORKSPACE}/wars"
            }
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
            environment {
                YOUR_CRED = credentials('ssh_to_docker_tomcat')
            }
            steps {
                script {
                    def credentialsMap = [:]
                    credentialsMap['YOUR_CRED_USR'] = YOUR_CRED_USR
                    credentialsMap['YOUR_CRED_PSW'] = YOUR_CRED_PSW

                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "ssh_to_docker_tomcat",
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '/home/wars/*.war',
                                        removePrefix: '/home/wars',
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
            post {
                always {
                    echo "The credentials are: ${YOUR_CRED_USR} and ${YOUR_CRED_PSW}"
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
