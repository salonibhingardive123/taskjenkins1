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
                sh '''
                echo "Stopping existing app on port $PORT"
                fuser -k $PORT/tcp || true
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                echo "===== STARTING APPLICATION ====="

                cd $WORKSPACE

                echo "Running from:"
                pwd
                ls -l

                echo "Starting app on port $PORT for $ENV"

                # Kill any leftover node processes (safety)
                pkill -f "node app.js" || true

                # Start app in background and detach from Jenkins
                nohup env PORT=$PORT ENV=$ENV node app.js > app.log 2>&1 &
                

                sleep 3

                echo "Running processes:"
                ps -ef | grep node

                echo "App log:"
                cat app.log || echo "No log found"

                echo "===== DONE ====="
                '''
            }
        }
    }
}