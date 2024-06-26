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

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def url = "http://52.233.163.41:8080/manager/text/deploy"
                    def tomcatUser = "deployer"  // Replace with your Tomcat username
                    def tomcatPassword = "password"  // Replace with your Tomcat user's password
                    def warFile = sh(returnStdout: true, script: 'ls target/*.war').trim()

                    def response = sh(returnStdout: true, script: """
                        curl -s -u ${tomcatUser}:${tomcatPassword} ${url}?path=/&war=file:${warFile}
                    """)

                    echo "Deployment response: ${response}"
                }
            }
        }
    }
}
