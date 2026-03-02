pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    parameters {
      string(name: 'sonar_IP', defaultValue: '100.54.191.181', description: 'IP of sonarqube')
    }
    environment {
      SONARQUBE_URL=${params.sonar_IP}
      SONARQUBE_TOKEN=credentials('Sonar-token')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/ShilpaLB28/Java-mini-project.git'
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                dir('webapp') {
                    sh """
                    mvn sonar:sonar \
                    -Dsonar.projectKey=JavaMiniProject \
                    -Dsonar.host.url=$SONARQUBE_URL \
                    -Dsonar.login=$SONARQUBE_TOKEN
                    """
                }
            }
        }
        stage('build') {
            steps {
                dir('webapp') {
                sh 'mvn clean package -DskipTests'
                }
            }
        }
    }
}