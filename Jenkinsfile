environment {
    PATH = "$WORKSPACE/conda/bin:$PATH"
    CONDA_UPLOAD_TOKEN = credentials('terradue-conda')
  }

pipeline {
    agent {
        docker { image 'conda-build:latest' }
    }
    stages {
        stage('Test') {
            steps {
                sh '''#!/usr/bin/env bash
                conda build --version
                conda --version
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''#!/usr/bin/env bash
                mkdir -p /home/jovyan/conda-bld/work
                cd $WORKSPACE
                mamba build .
                '''
            }
        }
        stage('Deploy') {            
            steps { 
                withCredentials([string(credentialsId: 'terradue-conda', variable: 'ANACONDA_API_TOKEN')]) {
                sh '''#!/usr/bin/env bash
                export PACKAGENAME=snap
                label=dev
                if [ "$GIT_BRANCH" = "master" ]; then label=main; fi
                anaconda upload --no-progress --user Terradue --label $label /srv/conda/envs/env_conda/conda-bld/*/$PACKAGENAME-*.tar.bz2
                '''}
            }
        }
    }
}
