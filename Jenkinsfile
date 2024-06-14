pipeline {
    agent any

    environment {
        SF_CLIENT_ID = '3MVG9PwZx9R6_UrctUSOwRPLgO5UmEb1M8MD6ykcMwS6WGG26Dr2LpOrYvRLgqp_.dRfG3LRTdWTaOjG12fF0'
        SF_CLIENT_SECRET = '034B1BF3FBFE1B84AC212EC24C51A9CBF73D223851BD1B65F95D4B8C055D3E02'
        SF_LOGIN_URL = 'https://jenkinstest-dev-ed.trailblaze.lightning.force.com'
    }

    stages {
        stage('Login') {
            steps {
                script {
                    echo 'Logging in...'
                    def response = sh(script: """
                        curl -X POST '${SF_LOGIN_URL}/services/oauth2/token?grant_type=client_credentials&client_id=${SF_CLIENT_ID}&client_secret=${SF_CLIENT_SECRET}' \
                        -H 'Content-Type: application/x-www-form-urlencoded'
                    """, returnStdout: true).trim()

                    def jsonResponse = readJSON text: response
                    env.SF_ACCESS_TOKEN = jsonResponse.access_token
                    env.SF_INSTANCE_URL = jsonResponse.instance_url

                    echo 'Logged in successfully.'
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                // Comandi di build
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // Comandi di test
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sh """
                echo 'instance_url=${SF_INSTANCE_URL}' > sfdx_url.txt
                echo 'access_token=${SF_ACCESS_TOKEN}' >> sfdx_url.txt
                sfdx auth:sfdxurl:store -f sfdx_url.txt
                sfdx force:source:deploy -p force-app -u SalesforceDX
                """
            }
        }
    }
}
