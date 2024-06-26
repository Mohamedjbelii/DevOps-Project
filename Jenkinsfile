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
                                           def tomcatUser = "deployer"  // Replace with your Tomcat username
                                           def tomcatPassword = "password"  // Replace with your Tomcat user's password
                                           def warFile = "/var/lib/jenkins/workspace/Build and deploy to tomcat/webapp/target/webapp.war"

                                           def response = sh(returnStdout: true, script: """
                                                curl -v -u "${tomcatUser}:${tomcatPassword}" -T "file=@${warFile}" "${url}"
                                           """)

                                           echo "Deployment response: ${response}"
                }
            }
        }
    }
}
