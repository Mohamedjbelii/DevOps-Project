pipeline {
    agent any
    tools {
        maven 'maven-3.9.8'
        jdk 'java-13'
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

        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }

        // stage('Archive Artifacts') {
        //     steps {
        //         archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
        //     }
        // }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
