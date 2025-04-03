pipeline {
    agent any
    environment {
        GITHUB_CREDENTIALS_ID = '55976c2e-1515-4e32-8bbd-d2b5a0ccacd8'  // Set your GitHub credentials ID
        GITHUB_REPO = 'RicardoArandaGonzalez/PORAmaster' // Replace with actual GitHub repo
        SOURCE_BRANCH = 'PORATesting' // Branch to merge from
        TARGET_BRANCH = 'PORAStaging'  // Branch to merge into
    }
    stages {
        stage('Checkout Source Branch') {
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

                    sh """
                    curl -X POST -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
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
        stage('Merge Pull Request (If Auto-Merge is Enabled)') {
            steps {
                script {
                    sh """
                    PR_NUMBER=\$(curl -s -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                        -H "Accept: application/vnd.github.v3+json" \
                        "https://api.github.com/repos/${GITHUB_REPO}/pulls?head=${SOURCE_BRANCH}" | \
                        jq '.[0].number')

                    if [ "\$PR_NUMBER" != "null" ]; then
                        curl -X PUT -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                             -H "Accept: application/vnd.github.v3+json" \
                             https://api.github.com/repos/${GITHUB_REPO}/pulls/\$PR_NUMBER/merge \
                             -d '{
                                   "commit_title": "Merging ${SOURCE_BRANCH} into ${TARGET_BRANCH}",
                                   "merge_method": "squash"
                               }'
                    else
                        echo "No pull request found, skipping merge."
                    fi
                    """
                }
            }
        }
    }
}
