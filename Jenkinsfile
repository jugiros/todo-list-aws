pipeline {
    agent any

    environment {
<<<<<<< HEAD
        PRODUCTION_STACK = "todo-list-aws-production"
        REGION           = "us-east-1"
=======
        STAGING_STACK = "todo-list-aws-staging"
        REGION        = "us-east-1"
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
    }

    stages {

        stage('Get Code') {
            steps {
<<<<<<< HEAD
                git branch: 'master',
                    url: 'https://github.com/jugiros/todo-list-aws'
                echo "Codigo descargado de rama master"
=======
                git branch: 'develop',
                    url: 'https://github.com/jugiros/todo-list-aws'
                echo "Codigo descargado de rama develop"
            }
        }

        stage('Static Test') {
            steps {
                sh '''
                    python3 -m flake8 src/ --exit-zero \
                        --format=pylint \
                        --output-file flake8-report.txt
                    python3 -m bandit -r src/ \
                        -f txt \
                        -o bandit-report.txt \
                        --exit-zero
                    echo "Informes generados: flake8-report.txt y bandit-report.txt"
                    cat flake8-report.txt
                    cat bandit-report.txt
                '''
            }
            post {
                always {
                    recordIssues(
                        tools: [flake8(pattern: 'flake8-report.txt')]
                    )
                    recordIssues(
                        tools: [pyLint(pattern: 'bandit-report.txt')]
                    )
                    archiveArtifacts artifacts: 'flake8-report.txt,bandit-report.txt',
                                     allowEmptyArchive: true
                }
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    sam build
                    sam deploy \
<<<<<<< HEAD
                        --config-env production \
=======
                        --config-env staging \
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
                        --no-confirm-changeset \
                        --no-fail-on-empty-changeset
                '''
                script {
                    env.BASE_URL = sh(
                        script: """
                            aws cloudformation describe-stacks \
<<<<<<< HEAD
                                --stack-name ${PRODUCTION_STACK} \
=======
                                --stack-name ${STAGING_STACK} \
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
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
<<<<<<< HEAD
                sh "python3 -m pytest test/integration/todoApiTest.py -v --tb=short -k 'test_api_listtodos or test_api_gettodo'"
=======
                sh "python3 -m pytest test/integration/todoApiTest.py -v --tb=short"
            }
        }

        stage('Promote') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-credentials',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh '''
                        git config user.email "jugiros1@gmail.com"
                        git config user.name "jugiros"
                        git remote set-url origin https://${GIT_TOKEN}@github.com/jugiros/todo-list-aws.git
                        git fetch origin
                        git checkout master
                        git merge develop --no-edit
                        git push origin master
                    '''
                }
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
<<<<<<< HEAD
            echo "Pipeline CD completado exitosamente - desplegado en produccion"
        }
        failure {
            echo "Pipeline CD fallo - revisar logs"
        }
    }
}
=======
            echo "Pipeline CI completado exitosamente - codigo promovido a master"
        }
        failure {
            echo "Pipeline CI fallo - revisar logs"
        }
    }
}
>>>>>>> 8e254a04342504058480ec4d9bd0a4300cf3a672
