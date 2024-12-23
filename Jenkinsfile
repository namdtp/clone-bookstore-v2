pipeline {
    agent any
    environment {
         BRANCH = "${params.branch ?: 'dev'}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Checking out branch: ${BRANCH}"
                    checkout([$class: 'GitSCM',
                        branches: [[name: "*/${BRANCH}"]],
                        userRemoteConfigs: [[url: 'https://github.com/namdtp/clone-bookstore-v2.git']]
                    ])
                }
            }
        }
        stage('Set Environment') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev') {
                        DEPLOY_ENV = 'Development'
                    } else if (env.BRANCH_NAME == 'staging') {
                        DEPLOY_ENV = 'Staging'
                    } else if (env.BRANCH_NAME == 'main') {
                        DEPLOY_ENV = 'Production'
                    } else {
                        error('Unknown branch!')
                    }
                    echo "Deploying to ${DEPLOY_ENV} environment."
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (DEPLOY_ENV == 'Development') {
                        sh './deploy-scripts/deploy-dev.sh'
                    } else if (DEPLOY_ENV == 'Staging') {
                        sh './deploy-scripts/deploy-staging.sh'
                    } else if (DEPLOY_ENV == 'Production') {
                        sh './deploy-scripts/deploy-prod.sh'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
