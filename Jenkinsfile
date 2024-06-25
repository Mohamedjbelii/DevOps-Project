pipeline {
    agent any
    tools {
        maven 'maven-3.9.8' // Adjust the Maven version as needed
        jdk 'java-13' // Adjust the Java version as needed
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
                // Add your test steps here
                echo 'Testing...'
            }
        }

//         stage('Archive Artifacts') {
//             steps {
//                 archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
//             }
//         }
        stage('Deploy') {
            steps {
                // Add your deploy steps here
                echo 'Deploying...'
            }
        }
    }
}
