pipeline{
    agent any

    environment{
        VENV_DIR = 'venv'
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
                    sh '''#!/bin/bash // REMOVED -el OPTIONS
                    python -m venv ${VENV_DIR}
                    source ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -e .
                    '''
                }
            }
        }
    }
}