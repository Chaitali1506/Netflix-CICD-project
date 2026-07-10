pipeline {

    agent any

    environment {

        SCANNER_HOME = tool 'sonar-scanner'

        IMAGE_NAME = "chaitali1513/netflix-clone"
        IMAGE_TAG  = "${BUILD_NUMBER}"

        SONAR_PROJECT_KEY  = "netflix-clone"
        SONAR_PROJECT_NAME = "Netflix Clone"

        TRIVY_FS_REPORT = "trivy-fs-report.html"
        TRIVY_IMAGE_REPORT = "trivy-image-report.html"

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
                        -Dsonar.sources=src/main/java \
                        -Dsonar.exclusions=target/**,.git/** \
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



        stage('Trivy File System Scan') {

            steps {

                sh """
                    trivy fs \
                    --severity HIGH,CRITICAL \
                    --format template \
                    --template '@contrib/html.tpl' \
                    -o ${TRIVY_FS_REPORT} .
                """

            }

            post {

                always {

                    publishHTML([

                        allowMissing: true,

                        alwaysLinkToLastBuild: true,

                        keepAll: true,

                        reportDir: '.',

                        reportFiles: "${TRIVY_FS_REPORT}",

                        reportName: 'Trivy File System Report'

                    ])

                }

            }

        }




        stage('Build Docker Image') {

            steps {

                sh '''
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .

                    docker tag \
                    ${IMAGE_NAME}:${IMAGE_TAG} \
                    ${IMAGE_NAME}:latest
                '''

            }

        }




        stage('Trivy Docker Image Scan') {

            steps {

                sh """

                    trivy image \
                    --severity HIGH,CRITICAL \
                    --format template \
                    --template '@contrib/html.tpl' \
                    -o ${TRIVY_IMAGE_REPORT} \
                    ${IMAGE_NAME}:${IMAGE_TAG}

                """

            }


            post {

                always {

                    publishHTML([

                        allowMissing: true,

                        alwaysLinkToLastBuild: true,

                        keepAll: true,

                        reportDir: '.',

                        reportFiles: "${TRIVY_IMAGE_REPORT}",

                        reportName: 'Trivy Image Scan Report'

                    ])

                }

            }

        }




        stage('Push Image to DockerHub') {

            steps {

                script {


                    docker.withRegistry(

                        'https://index.docker.io/v1/',

                        'docker-credentials-net'

                    ) {


                        sh """

                            docker push ${IMAGE_NAME}:${IMAGE_TAG}

                            docker push ${IMAGE_NAME}:latest

                        """

                    }

                }

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