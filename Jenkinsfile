pipeline {
    agent any
    environment {
        // Configura le tue credenziali e altre variabili necessarie qui
        SFDC_CLIENT_ID = credentials('sfdc-client-id')
        SFDC_CLIENT_SECRET = credentials('sfdc-client-secret')
        SFDC_USERNAME = credentials('sfdc-username')
        SFDC_PASSWORD = credentials('sfdc-password')
        SFDC_AUTH_URL = credentials('sfdc-auth-url')
    }
    stages {
        stage('Checkout') {
            steps {
                // Clona il repository e seleziona il branch "nuovo23"
                git branch: 'nuovo23', url: 'https://github.com/tuo-utente/tuo-repo.git'
            }
        }
        stage('Authenticate and Deploy to Salesforce') {
            steps {
                script {
                    // Ottieni il token di accesso
                    def response = sh(script: """
                        curl -X POST -d 'grant_type=password' \
                        -d 'client_id=$SFDC_CLIENT_ID' \
                        -d 'client_secret=$SFDC_CLIENT_SECRET' \
                        -d 'username=$SFDC_USERNAME' \
                        -d 'password=$SFDC_PASSWORD' \
                        $SFDC_AUTH_URL \
                        --silent | jq -r '.access_token'
                    """, returnStdout: true).trim()
                    
                    // Controlla se il token è stato ottenuto correttamente
                    if (response == null || response.isEmpty()) {
                        error "Failed to obtain access token"
                    }
                    
                    // Usa Salesforce CLI per eseguire il deploy
                    sh """
                        echo $response | sfdx auth:accesstoken:store -r $SFDC_AUTH_URL -a myOrg
                        sfdx force:source:deploy -p force-app
                    """
                }
            }
        }
        stage('Run Tests') {
            steps {
                // Esegue tutti i test nella org di Salesforce
                sh '''
                    sfdx force:apex:test:run --resultformat human --wait 10
                '''
            }
        }
    }
    post {
        always {
            // Pubblica i risultati dei test
            junit '**/test-result.xml'
        }
    }
}
