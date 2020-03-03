
node {
   stage('SCM') { // for display purposes
      git 'https://github.com/tesuvant/deployments_api_test.git'
   }
   stage('Build') {
       sh """#!/bin/bash -uex
         echo "Building ..."
         sleep 2
       """
   }
   stage('Deploy to Staging') {
     withCredentials([usernamePassword(credentialsId: 'ghe-token', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
         sh """#!/bin/bash -uex
         ID="null"
         while [ "\$ID" == "null" ]
         do
           ID=\$(curl -d '{"ref": "staging", "description": "Deploy request from ..."}' -X POST -H "Authorization: token $PASSWORD" 'https://api.github.com/repos/tesuvant/deployments_api_test/deployments' | /var/jenkins_home/jq -r .id)
           sleep 1
         done
         echo "Deploying ..."
         
         sleep 2
         
         curl -d '{"state": "in_progress", "environment": "staging", "description": "Pipeline running"}' \
         -X POST -H "Authorization: token $PASSWORD" \
         -H "application/vnd.github.flash-preview+json" \
         "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"
         
         sleep 3
         
         curl -d '{"state": "success", "environment": "staging", "description": "staging done", "log_url": "http://depl.logs", "environment_url": "http://service.in.staging.env"}' \
           -X POST -H "Authorization: token $PASSWORD" \
           -H "Accept: application/vnd.github.ant-man-preview+json" \
           "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"
         #curl -d '{"state": "error"}' -X POST -H "Authorization: token $PASSWORD" "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"
         
         
       """
     }

   }
   stage('Deployment OK?') {
       sh """#!/bin/bash -uex
        for i in {1..5}; do
            echo "Polling status \$i ..."
            echo "Deployment still ongoing"
            sleep 2
        done
        echo "Deploy OK!"
       """

   }
}
