pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/athabrani/sast-demo-app.git', branch: 'main'
            }
        }

        stage('Setup Virtualenv') {
            steps {
                sh '''
                    python3 -m venv $VENV_DIR
                    . $VENV_DIR/bin/activate
                    python -m pip install --upgrade pip
                    pip install bandit
                '''
            }
        }

        stage('SAST Analysis') {
            steps {
                sh '''
                    set -e  # Stop if any command fails
                    . $VENV_DIR/bin/activate

                    echo "Running Bandit scan..."
                    bandit -f xml -o bandit-output.xml -r . || echo "Bandit exited with non-zero code"

                    echo "Bandit output content:"
                    cat bandit-output.xml || echo "No bandit output file found"
                '''
                recordIssues tools: [scanForIssues(tool: 'Bandit', pattern: 'bandit-output.xml')]
            }
        }
    }
}


