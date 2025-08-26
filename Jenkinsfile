pipeline {
    agent any

    tools {
        ant 'AntLatest'   // Must match Jenkins Ant tool config name
    }

    parameters {
        choice(
            name: 'API_FILE',
            choices: [
                'apis/update-api_1.0.0.yaml',
                'apis/customer-api_2.0.0.yaml'
            ],
            description: 'Select API definition to validate & publish'
        )
        choice(
            name: 'PRODUCT_FILE',
            choices: [
                'products/umesh_1.0.0.yaml',
                'products/customer-product_2.0.0.yaml'
            ],
            description: 'Select Product definition to publish & deploy'
        )
        choice(
            name: 'CATALOG',
            choices: ['dev-catalog', 'test-catalog', 'prod-catalog'],
            description: 'Select target Catalog for deployment'
        )
        choice(
            name: 'ENV',
            choices: ['dev', 'test', 'prod'],
            description: 'Select target Environment'
        )
    }

    environment {
        // Credentials binding from Jenkins -> Credentials Manager
        APIC_CREDS = credentials('apic-creds') // ID must exist in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/apic-cicd.git'
            }
        }

        stage('Validate API') {
            steps {
                sh """
                  ant -Dapi.file=${params.API_FILE} \
                      -Dproduct.file=${params.PRODUCT_FILE} \
                      -Denv=${params.ENV} \
                      -Dapic.catalog=${params.CATALOG} \
                      -Dapic.username=${APIC_CREDS_USR} \
                      -Dapic.password=${APIC_CREDS_PSW} \
                      validate-api
                """
            }
        }

        stage('Validate Product') {
            steps {
                sh """
                  ant -Dapi.file=${params.API_FILE} \
                      -Dproduct.file=${params.PRODUCT_FILE} \
                      -Denv=${params.ENV} \
                      -Dapic.catalog=${params.CATALOG} \
                      -Dapic.username=${APIC_CREDS_USR} \
                      -Dapic.password=${APIC_CREDS_PSW} \
                      validate-product
                """
            }
        }

        stage('Publish & Deploy') {
            steps {
                sh """
                  ant -Dapi.file=${params.API_FILE} \
                      -Dproduct.file=${params.PRODUCT_FILE} \
                      -Denv=${params.ENV} \
                      -Dapic.catalog=${params.CATALOG} \
                      -Dapic.username=${APIC_CREDS_USR} \
                      -Dapic.password=${APIC_CREDS_PSW} \
                      deploy
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline finished: API=${params.API_FILE}, Product=${params.PRODUCT_FILE}, Catalog=${params.CATALOG}, Env=${params.ENV}"
        }
        success {
            echo "✅ Successfully published & deployed to ${params.ENV} (${params.CATALOG})"
        }
        failure {
            echo "❌ Deployment failed. Please check logs."
        }
    }
}
