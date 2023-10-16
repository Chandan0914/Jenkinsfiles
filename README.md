pipeline {
    agent any

    environment {
        SERVER_IP = credentials('your-credentials-id')
    }

    parameters {
        string(name: 'USERNAME', description: 'Server Username')
        password(name: 'PASSWORD', description: 'Server Password')
        string(name: 'SCRIPT_PATH', description: 'Script Path on the Server')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check if the script file exists
                    sh "sshpass -p '${PASSWORD}' ssh ${USERNAME}@${SERVER_IP} [ -f ${SCRIPT_PATH} ]"
                }
            }
        }

        stage('Execute Script') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    // Execute the script
                    sh "sshpass -p '${PASSWORD}' ssh ${USERNAME}@${SERVER_IP} bash ${SCRIPT_PATH}"
                }
            }
        }
    }
}
