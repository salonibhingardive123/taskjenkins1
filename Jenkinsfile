pipeline {
    agent any

    environment {
        APP_NAME = "node-app"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Set Environment Based on Branch') {
            steps {
                script {
                    if (env.BRANCH_NAME == "dev") {
                        env.PORT = "5000"
                        env.ENV = "DEV"
                    } else if (env.BRANCH_NAME == "qa") {
                        env.PORT = "5001"
                        env.ENV = "QA"
                    } else {
                        env.PORT = "5002"
                        env.ENV = "PROD"
                    }
                }
            }
        }

        stage('Stop Existing App') {
            steps {
                script {
                    sh """
                    echo "Stopping existing app on port ${PORT}"
                    fuser -k ${PORT}/tcp || true
                    """
                }
            }
        }

        stage('Run Application') {
            steps {
                sh '''
            echo "===== DEBUG START ====="
            echo "Workspace is: $WORKSPACE"
        pwd
        ls -l

        cd $WORKSPACE

        echo "After cd:"
        pwd
        ls -l

        echo "Starting app..."
        nohup env PORT=$PORT ENV=$ENV node app.js > app.log 2>&1 &

        sleep 2

        echo "Running processes:"
        ps -ef | grep node

        echo "App log output:"
        cat app.log || echo "No log file found"

        echo "===== DEBUG END ====="
        '''
    }
}
    }
}