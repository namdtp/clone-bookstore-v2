pipeline {
    agent any

    parameters {
        string(name: 'branch', defaultValue: 'main', description: 'Branch to build and deploy')
    }

    environment {
        NODE_VERSION = '16'   // Node.js version
        NPM_CACHE_DIR = '.npm' // NPM cache directory
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Branch provided: ${params.branch}"
                    checkout([$class: 'GitSCM',
                              branches: [[name: "*/${params.branch}"]],
                              userRemoteConfigs: [[url: 'https://github.com/namdtp/clone-bookstore-v2.git']]
                    ])
                }
            }
        }

        stage('Set Environment') {
            steps {
                script {
                    echo "Setting environment for branch: ${params.branch}"
                }
            }
        }

        stage('Install Dependencies') {
        steps {
            script {
                echo "Installing dependencies for Node.js version: ${env.NODE_VERSION}"
                sh """
                    # Install Node.js
                    curl -fsSL https://deb.nodesource.com/setup_${env.NODE_VERSION}.x | sudo -E bash -
                    sudo apt-get install -y nodejs

                    # Install dependencies
                    npm install
                """
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo "Running tests..."
                    sh "npm test" // Thay đổi nếu bạn có lệnh kiểm tra cụ thể
                }
            }
        }

        stage('Build Application') {
            steps {
                script {
                    echo "Building the application..."
                    sh "npm run build" // Lệnh build ứng dụng
                }
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'dev'
                    branch 'staging'
                    branch 'main'
                }
            }
            steps {
                script {
                    if (params.branch == 'dev') {
                        echo "Deploying to Development Environment"
                        sh "./deploy-dev.sh"
                    } else if (params.branch == 'staging') {
                        echo "Deploying to Staging Environment"
                        sh "./deploy-staging.sh"
                    } else if (params.branch == 'main') {
                        echo "Deploying to Production Environment"
                        sh "./deploy-prod.sh"
                    } else {
                        error "Unknown branch for deployment!"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline execution completed."
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline execution failed."
        }
    }
}
