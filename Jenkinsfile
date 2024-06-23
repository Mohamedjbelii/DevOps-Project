pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Mohamedjbelii/hello-world.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                // Add your build steps here
                echo 'Building...'
            }
        }

        stage('Test') {
            steps {
                // Add your test steps here
                echo 'Testing...'
            }
        }

        stage('Deploy') {
            steps {
                // Add your deploy steps here
                echo 'Deploying...'
            }
        }
    }
}
