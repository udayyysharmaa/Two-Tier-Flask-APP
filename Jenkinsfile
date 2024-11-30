pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    environment {
        SONAR_HOME = tool 'sonar-scanner' // Corrected the typo
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/udayyysharmaa/Two-Tier-Flask-APP.git'
            }
        }
        stage('Scan the File') {
            steps {
                sh 'trivy fs --format json -o filetwotier.json .'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') { // Ensure 'sonar' matches the SonarQube server configuration in Jenkins
                    sh '''
                    ${SONAR_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=mysqlproject \
                    -Dsonar.projectKey=mysqlproject \
                    -Dsonar.java.binaries=target
                    '''
                }
            }
        }
        stage('Qualtiy Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage('Creating a docker image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub') {
                        sh 'docker build -t onlinelearningofficial/mysqltwitier:v1 .'
                    }
                }
            }
        }
        stage('Iamge Scan') {
            steps {
                sh 'trivy image --format json -o filetwotierimage.json onlinelearningofficial/mysqltwitier:v1'
            }
        }
        stage('Push to docker hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub') {
                        sh 'docker push onlinelearningofficial/mysqltwitier:v1'
                    }
                }
            }
        }
        stage('Build the Project') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub') {
                        sh 'docker-compose up -d'
                    }
                }
            }
        }
    }
}
