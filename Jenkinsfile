pipeline {
    agent any
    stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/ShilpaLB28/maven-project.git', credentialsId: 'git'
            }
        }
        stage('build') {
            steps {
                echo "build stage"
            }
        }
        stage('test') {
            steps {
                echo "test stage"
            }
        }
    }
}