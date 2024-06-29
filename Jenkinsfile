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

//
//                    def warFiles = sh(script: "ls ${WARDIR}/*.war", returnStdout: true).trim()
//                    if (warFiles) {
//                        echo "WAR files found: ${warFiles}"
//                    } else {
//                        error "No WAR files found in ${WARDIR}"
//                    }
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "dokerhost",
                                transfers: [
                                        sshTransfer(sourceFiles: '${WARDIR}/*.war'),
                                        sshTransfer(
                                        execCommand: '''
                                            docker ps -a 
                                            echo "Checking /usr/local/tomcat/webapps directory"

                                            ls -a /usr/local/tomcat/webapps
                                            echo "Copying WAR files to container"
                                            docker cp /usr/local/tomcat/webapps/*.war 753ef1dae659:/usr/local/tomcat/webapps/
                                            docker exec ${CONTAINER_ID} ls -la /usr/local/tomcat/webapp
                                            
                                            echo "Restarting Tomcat"
                                            docker exec  753ef1dae659 sh -c "catalina.sh stop"
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
