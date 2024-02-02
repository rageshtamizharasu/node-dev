pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git branch: 'master', url: 'https://github.com/rageshtamizharasu/node-dev.git'
                sh 'ls'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarqube'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=node.js-app"
                    }
                }
            }
        }
        stage('Docker Build Image') {
            steps {
                script {
                    sh 'docker build -t ragesh2u/multi:v2 .'
                    sh 'docker images'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                        sh "docker login -u ragesh2u -p ${dockerPassword}"
                        sh 'docker push ragesh2u/multi:v2'
                        sh 'docker rmi ragesh2u/multi:v2'
                    }
                }
            }
        }
        stage('Deploy on k8s') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[ credentialsId: 'kubernetes', namespace: 'ms' ]]) {
                        sh 'kubectl apply -f kube.yaml'
                    }
                }
            }
        }
    }
}
