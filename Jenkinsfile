pipeline {
    agent any

    environment {
        VENV_PATH = "${WORKSPACE}/venv"
        PYTHON = "${WORKSPACE}/venv/bin/python"
        PIP = "${WORKSPACE}/venv/bin/pip"
    }

    stages {

        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh """
                python3 -m venv ${VENV_PATH}
                ${PIP} install --upgrade pip
                ${PIP} install -r requirements.txt
                """
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh """
                ${PYTHON} -m unittest discover -s .
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh """
                mkdir -p ${WORKSPACE}/python-app-deploy
                cp ${WORKSPACE}/app.py ${WORKSPACE}/python-app-deploy/
                """
            }
        }

        stage('Run Application') {
            steps {
                echo 'Running application...'
                sh """
                nohup ${PYTHON} ${WORKSPACE}/python-app-deploy/app.py \
                    > ${WORKSPACE}/python-app-deploy/app.log 2>&1 &
                echo \$! > ${WORKSPACE}/python-app-deploy/app.pid
                """
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing application...'
                sh """
                ${PYTHON} ${WORKSPACE}/test_app.py
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
