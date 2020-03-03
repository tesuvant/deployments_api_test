
node {
   stage('SCM') { // for display purposes
      git 'https://github.com/tesuvant/deployments_api_test.git'
   }
   stage('Build') {
       sh """#!/bin/bash -uex
         echo "Building ..."
         sleep 3
       """
   }
   stage('Start deploy') {
     withCredentials([usernamePassword(credentialsId: 'ghe-token', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
         sh """#!/bin/bash -uex
         ID=\$(curl -d '{"ref": "staging"}' -X POST -H "Authorization: token $PASSWORD" 'https://api.github.com/repos/tesuvant/deployments_api_test/deployments' | jq -r .id)
         echo \$ID > id
         echo "Deploying ..."
         sleep 10
         curl -d'{"state": "queued"}' -X POST -H "Authorization: token $PASSWORD" 'https://api.github.com/repos/tesuvant/deployments_api_test/deployments/\$ID/statuses'
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
