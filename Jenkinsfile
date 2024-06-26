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
                                    def url = "http://52.233.163.41:8080/manager/text/deploy?path=/webapp&update=true"
                                    def tomcatUser = "deployer"
                                    def tomcatPassword = "password"
                                    def warFile = sh(returnStdout: true, script: 'ls target/*.war').trim()

                                    def response = sh(returnStdout: true, script: '''
                                        curl -T $warFile "$url" --user $tomcatUser:$tomcatPassword
                                    ''').trim()

                                    echo "Deployment response: $response"
                                }
            }
        }
    }
}
