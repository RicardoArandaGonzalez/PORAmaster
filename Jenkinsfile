pipeline {
    agent any
    environment {
        GITHUB_CREDENTIALS_ID = 'github_token'  // Set your GitHub credentials ID
        GITHUB_REPO = 'RicardoArandaGonzalez/PORAmaster' // Replace with actual GitHub repo
        SOURCE_BRANCH = 'PORATesting' // Branch to merge from
        TARGET_BRANCH = 'PORAStaging'  // Branch to merge into
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: "*/${SOURCE_BRANCH}"]],
                    userRemoteConfigs: [[
                        url: "https://github.com/${GITHUB_REPO}.git",
                        credentialsId: "${GITHUB_CREDENTIALS_ID}"
                    ]]
                ]
            }
        }
        stage('Create Pull Request') {
            steps {
                script {
                    def prTitle = "Merge ${SOURCE_BRANCH} into ${TARGET_BRANCH}"
                    def prBody = "This is an automated PR created by Jenkins."
        
                    withCredentials([string(credentialsId: 'github_token', variable: 'GITHUB_TOKEN')]) {
                        sh """
                        curl -X POST -H "Authorization: token ${GITHUB_TOKEN}" \
                             -H "Accept: application/vnd.github.v3+json" \
                             https://api.github.com/repos/${GITHUB_REPO}/pulls \
                             -d '{
                                   "title": "${prTitle}",
                                   "head": "${SOURCE_BRANCH}",
                                   "base": "${TARGET_BRANCH}",
                                   "body": "${prBody}"
                               }'
                        """
                    }
                }
            }
        }
    }
}

