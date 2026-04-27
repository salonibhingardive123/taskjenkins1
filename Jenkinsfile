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

        # This variable prevents Jenkins from killing the process after the build ends
        export JENKINS_NODE_COOKIE=dontKillMe 

        # Use -f to ensure fuser actually kills the process
        fuser -k $PORT/tcp || true

        # Start the app
        setsid nohup env PORT=$PORT ENV=$ENV node app.js > app.log 2>&1 < /dev/null &

        sleep 5
        echo "Check if process is still alive:"
        pgrep -fl "node app.js" || echo "Process failed to start!"
        
        echo "===== DONE ====="
        '''
    }
}
    }
}