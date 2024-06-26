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
def tomcatUrl = "http://52.233.163.41:8080"
                    def contextPath = "/webapp"
                    def update = "true"
                    def tomcatUser = "deployer"
                    def tomcatPassword = "password"
                    def warFilePath = "/var/lib/jenkins/workspace/Build and deploy to tomcat/webapp/target/webapp.war"

                    def url = "${tomcatUrl}/manager/text/deploy?path=${contextPath}&update=${update}"

                    // Quote warFilePath to handle spaces
                    def response = sh(returnStdout: true, script: """
                        curl -v -u ${tomcatUser}:${tomcatPassword} -T '${warFilePath}' '${url}'
                    """).trim()

                    echo "Deployment response: ${response}"
                                }
            }
        }
    }
}
