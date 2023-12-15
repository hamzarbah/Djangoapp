pipeline {
    agent any
    
    stages {
        stage('Git') {
            steps {
                git branch: 'master', credentialsId: 'gitpass', url: 'https://github.com/hamzarbah/Djangoapp.git'
            }
        }
        stage('Build Django App') {
            steps {
                script {
                    sh "pip install -r requirements.txt --user"

                }
            }
        }
        stage('Lint Dockerfile') {
            steps {
                script {
                    def hadolintCmd = "hadolint Dockerfile || true"
                    def lintResult = sh(script: hadolintCmd, returnStatus: true)

                    if (lintResult != 0) {
                        echo "Linting warnings found, but continuing with the pipeline."
                    }
                }
            }
        }
         stage('Docker image') {
            steps {
                script {
                    sh 'docker build -t hamzarbah/app:Django .'
                }
            }
         }
          stage('Push image to hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpass')]) {
                        sh 'docker login -u hamzarbah -p ${dockerpass}'
                        sh 'docker push hamzarbah/app:Django'
                    }
                }
            }
          }
                  stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker compose up -d'
                }
            }
        }
 stage('Prometheus') {
            steps {
                script {
                    def prometheusResponse = sh(script: "curl -s 'http://192.168.33.10:9090/api/v1/query?query=up'", returnStdout: true).trim()
                    echo "Prometheus Response: ${prometheusResponse}"
                    echo "curl -s 192.168.33.10:9090"
                }
            }
        }
         

    }
}
