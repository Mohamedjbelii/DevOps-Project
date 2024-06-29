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

            steps (){
                sshPublisher(publishers:
                        [sshPublisherDesc(configName: 'dokerhost',
                                transfers: [
                                        sshTransfer(cleanRemote: false,
                                                excludes: '',
                                                execCommand: '',
                                                execTimeout: 120000,
                                                flatten: false,
                                                makeEmptyDirs: false,
                                                noDefaultExcludes: false,
                                                patternSeparator: '[, ]+',
                                                remoteDirectory: '/usr/local/tomcat/webapps',
                                                remoteDirectorySDF: false,
                                                removePrefix: '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/wars/',
                                                sourceFiles: '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/wars/webapp.war')],
                                usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])

            }
        }

        stage('Notification') {
            steps {
                echo 'Notifying...'
            }
        }
    }
}
