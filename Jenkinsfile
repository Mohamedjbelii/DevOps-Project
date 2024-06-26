    pipeline {
        agent any
        tools {
            maven 'maven-3.9.8'
            jdk 'java-13'
        }
            environment {
                TOMCAT_CREDENTIALS_ID = 'tomact host ssh' // Ensure this matches your credentials ID in Jenkins
                TOMCAT_URL = 'http://52.233.178.153:8080'
            }

        stages {
            stage('Checkout SCM') {
                steps {
                    checkout scm
                }
            }

            stage('Build') {
                steps {
                    sh 'mvn clean install'
                }
            }

            stage('Deploy to Tomcat') {
                steps {
                script {
                    def warFile = sh(script: 'ls /var/lib/jenkins/workspace/BuildandDeployOnContainerUI/webapp/target/*.war', returnStdout: true).trim()
                    if (warFile) {
                        deploy adapters: [tomcat9(credentialsId: TOMCAT_CREDENTIALS_ID, path: '', url: TOMCAT_URL)], contextPath: '', war: warFile
                    } else {
                        error 'WAR file not found'
                    } }
                }
            }
        }
    }
