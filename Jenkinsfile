
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
   stage('Start deploy') {
     withCredentials([usernamePassword(credentialsId: 'ghe-token', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
         sh """#!/bin/bash -uex
         ID="null"
         while [ "\$ID" == "null" ]
         do
           ID=\$(curl -d '{"ref": "staging"}' -X POST -H "Authorization: token $PASSWORD" 'https://api.github.com/repos/tesuvant/deployments_api_test/deployments' | /var/jenkins_home/jq -r .id)
           sleep 1
         done
         echo "Deploying ..."
         sleep 2
         curl -d '{"state": "pending", "environment": "qa", "description": "Pipeline running"}' -X POST -H "Authorization: token $PASSWORD" "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"
         sleep 3
         curl -d '{"state": "success", "environment": "qa", "description": "All done", "target_url": "www.google.com"}' -X POST -H "Authorization: token $PASSWORD" "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"         curl -d '{"state": "error"}' -X POST -H "Authorization: token $PASSWORD" "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"
         #curl -d '{"state": "failure"}' -X POST -H "Authorization: token $PASSWORD" "https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses"

         
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
