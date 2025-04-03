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
                    response=\$(curl -s -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                         -H "Accept: application/vnd.github.v3+json" \
                         https://api.github.com/repos/${GITHUB_REPO}/pulls \
                         -d '{
                               "title": "${prTitle}",
                               "head": "${SOURCE_BRANCH}",
                               "base": "${TARGET_BRANCH}",
                               "body": "${prBody}"
                           }')
                    
                    echo "Create PR Response: \$response"
                    """
                }
            }
        }
        stage('Merge Pull Request (If Auto-Merge is Enabled)') {
            steps {
                script {
                    sh """
                    # Fetch the pull requests with the source branch
                    response=\$(curl -s -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                        -H "Accept: application/vnd.github.v3+json" \
                        "https://api.github.com/repos/${GITHUB_REPO}/pulls?head=${SOURCE_BRANCH}")
                    
                    echo "Raw Response: \$response"
                    
                    # Check if response contains a pull request
                    PR_NUMBER=\$(echo \$response | jq '.[0].number' 2>/dev/null)
                    
                    if [ -z "\$PR_NUMBER" ]; then
                        echo "No pull request found for branch ${SOURCE_BRANCH}"
                        exit 0
                    fi
                    
                    echo "PR Number: \$PR_NUMBER"
                    
                    # Merge the pull request (if PR exists)
                    merge_response=\$(curl -X PUT -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                        -H "Accept: application/vnd.github.v3+json" \
                        "https://api.github.com/repos/${GITHUB_REPO}/pulls/\$PR_NUMBER/merge" \
                        -d '{
                            "commit_title": "Merging ${SOURCE_BRANCH} into ${TARGET_BRANCH}",
                            "merge
