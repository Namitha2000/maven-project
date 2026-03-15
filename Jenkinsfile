pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
        git 'Default'
    }
    parameters {
        string(name: 'sonar_IP', defaultValue: '13.63.34.172', description: 'IP of sonarqube')
        string(name: 'nexus_IP', defaultValue: '16.16.195.162', description: 'IP of Nexus')
        string(name: 'deploy_IP', defaultValue:'13.50.101.149', description: 'IP of Deploy Server')   
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
       
        stage('Deploy Application to EC2') {
           steps {
             sshagent(['deploy-server']) {
                    sh """
                    scp -o StrictHostKeyChecking=no \
                        webapp/target/webapp.war \
                        ubuntu@${params.deploy_IP}:/tmp/webapp.war

                    ssh -o StrictHostKeyChecking=no ubuntu@${params.deploy_IP} '
                        sudo cp /tmp/webapp.war /var/lib/tomcat10/webapps/webapp.war &&
                        sudo systemctl restart tomcat10
                    '
                    """
                }
           }
        }
    }
}
