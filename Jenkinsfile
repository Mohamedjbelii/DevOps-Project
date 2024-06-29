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
                    sh 'ls -a ${WARDIR} '


                }
            }
        }

        stage('Deploy to Tomcat') {

            steps ([$class: 'BapSshPromotionPublisherPlugin']){
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "dokerhost",
                                transfers: [
                                        sshTransfer(sourceFiles: '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/wars/webapp.war')
                                ],
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
