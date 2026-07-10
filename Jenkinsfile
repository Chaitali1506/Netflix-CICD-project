pipeline {

    agent any

    environment {

        SCANNER_HOME = tool 'sonar-scanner'

        IMAGE_NAME = "chaitali1513/netflix-clone"
        IMAGE_TAG  = "${BUILD_NUMBER}"

        SONAR_PROJECT_KEY  = "netflix-clone"
        SONAR_PROJECT_NAME = "Netflix Clone"

    }


    stages {


        stage('Checkout Source Code') {

            steps {

                checkout scm

                sh '''
                    echo "Checking project files"
                    ls -la
                '''

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
                    -Dsonar.exclusions=.git/**,target/** \
                    -Dsonar.sourceEncoding=UTF-8

                    """

                }

            }

        }



        stage('Quality Gate') {

            steps {

                timeout(time: 5, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: false

                }

            }

        }



        stage('Trivy File System Scan') {

            steps {

                sh '''

                echo "Running Trivy filesystem scan"

                trivy fs \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-fs-report.txt \
                . || true

                '''

            }

        }




        stage('Build Docker Image') {

            steps {

                sh '''

                echo "Building Docker Image"

                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} .

                docker tag \
                ${IMAGE_NAME}:${IMAGE_TAG} \
                ${IMAGE_NAME}:latest


                docker images

                '''

            }

        }




        stage('Trivy Docker Image Scan') {

            steps {

                sh '''

                echo "Running Trivy Image Scan"

                trivy image \
                --severity HIGH,CRITICAL \
                --format table \
                -o trivy-image-report.txt \
                ${IMAGE_NAME}:${IMAGE_TAG} || true

                '''

            }

        }




        stage('Push Image to DockerHub') {

            steps {

                script {

                    echo "Pushing Docker Image"


                    docker.withRegistry(

                        'https://index.docker.io/v1/',

                        'docker-credentials-net'

                    ) {


                        sh '''

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}

                        docker push ${IMAGE_NAME}:latest

                        '''

                    }

                }

            }

        }
        stage('Deploy to k3s') {
            steps {
               sh '''
                   kubectl apply -f k8s/deployment.yaml
                   kubectl apply -f k8s/service.yaml

                   kubectl rollout status deployment/netflix-deployment

                   kubectl get pods
                   kubectl get svc
                '''
            }
        }


    }



    post {


        success {

            echo "Pipeline completed successfully"

        }


        failure {

            echo "Pipeline failed. Check console logs"

        }


        always {

            echo "Pipeline execution completed"

        }

    }

}