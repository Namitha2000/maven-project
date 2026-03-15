pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
        git 'Default'
    }
    parameters {
        string(name: 'sonar_IP', defaultValue: '13.50.246.94', description: 'IP of sonarqube')
        string(name: 'nexus_IP', defaultValue: '16.16.195.162', description: 'IP of Nexus')
    }
    environment {
        SONARQUBE_URL = "http://${params.sonar_IP}:9000"
        SONARQUBE_TOKEN = credentials('sonar-token')
        NEXUS_URL = "http://${params.nexus_IP}:8081/repository/maven-releases-repo/"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Namitha2000/maven-project.git'
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                dir('webapp') {
                    sh """
                    mvn sonar:sonar \
                    -Dsonar.projectKey=MavenProject \
                    -Dsonar.host.url=$SONARQUBE_URL \
                    -Dsonar.login=$SONARQUBE_TOKEN
                    """
                }
            }
        }
        stage('Build') {
            steps {
                dir('webapp') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        stage('Deploy to Nexus') {
            steps {
                dir('webapp') {
                    sh """
                    mvn deploy -DskipTests \
                    -DaltDeploymentRepository=nexus-releases::default::${NEXUS_URL}
                    """
                }
            }
        }
    }
}
