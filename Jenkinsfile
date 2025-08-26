pipeline {
    agent any

    tools {
        ant 'Ant-1.10.15'   // use the name from Jenkins Tool Config
    }

    parameters {
        choice(name: 'API_FILE', choices: [
            'apis/update-api_1.0.0.yaml',
            'apis/customer-api_2.0.0.yaml'
        ], description: 'Select the API file to deploy')

        choice(name: 'PRODUCT_FILE', choices: [
            'products/umesh_1.0.0.yaml',
            'products/customer-product_2.0.0.yaml'
        ], description: 'Select the Product file to deploy')

        choice(name: 'CATALOG', choices: [
            'dev-catalog',
            'test-catalog',
            'prod-catalog'
        ], description: 'Select the catalog for deployment')

        choice(name: 'ENV', choices: [
            'dev',
            'test',
            'prod'
        ], description: 'Select the target environment')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy with Ant') {
            steps {
                sh """
                  ant -f build.xml \\
                      -Dapi.file=${params.API_FILE} \\
                      -Dproduct.file=${params.PRODUCT_FILE} \\
                      -Dapic.catalog=${params.CATALOG} \\
                      -Denvironment=${params.ENV}
                """
            }
        }
    }
}
