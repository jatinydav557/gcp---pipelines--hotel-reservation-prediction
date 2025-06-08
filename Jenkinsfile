pipeline{
    agent any

    environment{
        VENV_DIR = 'venv'
        GCP_PROJECT = "sigma-scheduler-461007-u2"
        GCLOUD_PATH = "/var/jenkins_home/google-cloud-sdk/bin" // Ensure this path is correct on your Jenkins agent
    }

    stages{
        stage('Cloning github repo to jenkins'){
            steps{
                script{
                    echo 'Cloning github repo to jenkins........'
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/jatinydav557/gcp---pipelines--hotel-reservation-prediction.git']])
                }
            }
        }

        stage('Setting up our virtual environment and installing dependencies'){
            steps{
                script{
                    echo 'Setting up our virtual environment and installing dependencies'
                    sh '''#!/bin/bash
                    python -m venv ${VENV_DIR}
                    source ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -e .
                    '''
                }
            }
        }

        stage('Building and pushing docker image to GCR'){
            steps{
                withCredentials([file(credentialsId : 'gcp-key' ,variable : 'GOOGLE_APPLICATION_CREDENTIALS')]){
                    script{
                        echo 'Building and pushing docker image to GCR......'
                        sh '''#!/bin/bash
                        # Add gcloud to PATH for this script block
                        export PATH=$PATH:${GCLOUD_PATH}

                        # Authenticate gcloud using the service account key
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS} --project=${GCP_PROJECT}

                        # Set the gcloud project (this is redundant but harmless if set in activate-service-account)
                        gcloud config set project ${GCP_PROJECT}

                        # Configure Docker to use gcloud for authentication with GCR
                        gcloud auth configure-docker --quiet

                        # Build the Docker image
                        docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .

                        # Push the Docker image to Google Container Registry
                        docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy to google cloud run'){
            steps{
                withCredentials([file(credentialsId : 'gcp-key' ,variable : 'GOOGLE_APPLICATION_CREDENTIALS')]){
                    script{
                        echo 'Deploy to google cloud run......'
                        sh '''#!/bin/bash
                        # Add gcloud to PATH for this script block
                        export PATH=$PATH:${GCLOUD_PATH}

                        # Authenticate gcloud using the service account key
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS} --project=${GCP_PROJECT}

                        # Set the gcloud project (this is redundant but harmless)
                        gcloud config set project ${GCP_PROJECT}

                        # Deploy the image to Google Cloud Run
                        gcloud run deploy ml-project \\
                            --image=gcr.io/${GCP_PROJECT}/ml-project:latest \\
                            --platform=managed \\
                            --region=us-central1 \\
                            --allow-unauthenticated
                        '''
                    }
                }
            }
        }
    }
}