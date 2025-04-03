stage('Merge Pull Request (If Auto-Merge is Enabled)') {
    steps {
        script {
            sh """
            # Fetch the pull requests with the source branch
            response=\$(curl -s -H "Authorization: token ${GITHUB_CREDENTIALS_ID}" \
                -H "Accept: application/vnd.github.v3+json" \
                "https://api.github.com/repos/${GITHUB_REPO}/pulls?head=${SOURCE_BRANCH}")
            
            # Print the raw response to debug the structure
            echo "Raw Response: \$response"
            
            # Check if response is an array
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
                    "merge_method": "squash"
                }')
            
            echo "Merge Response: \$merge_response"
            """
        }
    }
}
