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
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: '/var/lib/jenkins/workspace/BuildandDeployOnContainerUI/webapp/target/*.war'
            }
        }

        stage('Deployment') {
            steps {
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'tomact host ssh	',
                        url: 'http://52.233.178.153:8080/',
                        contextPath: 'webapp',
                        war: 'target/*.war'
                    )
                ]
            }
        }

        stage('Notification') {
            steps {
            	                echo 'Notifiying...'
            }
        }
    }
}
