pipeline {
    agent any

    environment {
        PRODUCTION_STACK = "todo-list-aws-production"
        REGION           = "us-east-1"
    }

    stages {

        stage('Get Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/jugiros/todo-list-aws'
                echo "Codigo descargado de rama master"
                sh '''
                    curl -o samconfig.toml \
                        https://raw.githubusercontent.com/jugiros/todo-list-aws-config/production/samconfig.toml
                    echo "samconfig.toml descargado de rama production"
                    cat samconfig.toml
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    sam build
                    sam deploy \
                        --config-env production \
                        --no-confirm-changeset \
                        --no-fail-on-empty-changeset
                '''
                script {
                    env.BASE_URL = sh(
                        script: """
                            aws cloudformation describe-stacks \
                                --stack-name ${PRODUCTION_STACK} \
                                --query 'Stacks[0].Outputs[?OutputKey==`BaseUrlApi`].OutputValue' \
                                --region ${REGION} \
                                --output text
                        """,
                        returnStdout: true
                    ).trim()
                    echo "BASE_URL obtenida: ${env.BASE_URL}"
                }
            }
        }

        stage('Rest Test') {
            environment {
                BASE_URL = "${env.BASE_URL}"
            }
            steps {
                sh "echo 'Ejecutando pytest contra: ${BASE_URL}'"
                sh "python3 -m pytest test/integration/todoApiTest.py -v --tb=short -k 'test_api_listtodos or test_api_gettodo'"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Pipeline CD completado exitosamente - desplegado en produccion"
        }
        failure {
            echo "Pipeline CD fallo - revisar logs"
        }
    }
}