pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        IMAGE_NAME = "chaitali1513/netflix-clone"
        IMAGE_TAG  = "${BUILD_NUMBER}"

        SONAR_PROJECT_KEY  = "netflix-clone"
        SONAR_PROJECT_NAME = "Netflix Clone"

        TRIVY_REPORT = "trivy-image-report.html"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Source Code') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
               withSonarQubeEnv('sonar-server') {
                   sh """
                       ${SCANNER_HOME}/bin/sonar-scanner \
                       -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                       -Dsonar.projectName="${SONAR_PROJECT_NAME}" \
                       -Dsonar.sources=. \
                       -Dsonar.sourceEncoding=UTF-8
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Publish Trivy Report') {
            steps {
                publishHTML(target: [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'trivy-image-report.html',
                    reportName: 'Trivy Image Scan Report'
                ])
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-token') {
                        sh '''
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker push ${IMAGE_NAME}:latest
                        '''
                    }
                }
            }
        }

        