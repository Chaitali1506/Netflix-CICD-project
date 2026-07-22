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

                        

                        '''

                    }

                }

            }

        }
        stage('Deploy to k3s') {
            steps {
                script {
                    sh '''
                    sed -i "s/IMAGE_TAG/${IMAGE_TAG}/g" k3s/deployment.yaml
                    cat k3s/deployment.yaml
                    '''
                    sshagent(credentials: ['K3S-key']) {
                        sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@3.110.229.177 "mkdir -p ~/netflix/k8s"

                        scp -o StrictHostKeyChecking=no k3s/*.yaml \
                        ubuntu@3.110.229.177:~/netflix/k8s/

                        ssh -o StrictHostKeyChecking=no ubuntu@3.110.229.177 << EOF
                            cd ~/netflix/k8s
                            sudo kubectl apply -f deployment.yaml
                            sudo kubectl apply -f service.yaml
                            sudo kubectl rollout status deployment/netflix-deployment
                            sudo kubectl get pods
                            sudo kubectl get svc
                        '''
                    }
                }
            }
        }
    }
    post {
    success {
        emailext(
            subject: "SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
            body: "Build Successful\n\nJob: ${JOB_NAME}\nBuild: ${BUILD_NUMBER}\n${BUILD_URL}",
            to: "chaitalikale1512003@gmail.com"
        )
    }

    failure {
        emailext(
            subject: "FAILED: ${JOB_NAME} #${BUILD_NUMBER}",
            body: "Build Failed\n\nJob: ${JOB_NAME}\nBuild: ${BUILD_NUMBER}\n${BUILD_URL}",
            to: "chaitalikale1512003@gmail.com"
        )
    }

    always {
        echo "Pipeline execution completed"
    }
    }

}
