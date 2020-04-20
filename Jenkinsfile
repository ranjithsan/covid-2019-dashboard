#!groovy​
pipeline {
    agent any

    environment {
      CUSTOMER_NAME = "covidadmin"
      PROJECT_NAME = "Covid-2019"
      def sshCopy="covidadmin@covid19-data.eastus.cloudapp.azure.com"
    }
      stages('deploy') {

         stage('NPM test + install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy Customers') {
          parallel {
            stage('Deploy Covid-2019') {
              stages {
                stage(":dev:") {
                  when {
                    anyOf {
                      branch 'develop'
                      branch 'feature/build/*'
                       }
                     }
                     steps {
                       sshagent (credentials: ['ssh-covid-connect']) {

                        sh "ssh ${sshCopy} 'mkdir -p /home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/dev'"
                        sh "ssh ${sshCopy} 'rm -rf /home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/dev/*'"
                        sh "scp -r ./public ${sshCopy}:/home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/dev/"
                        sh "scp -r ./package.json ${sshCopy}:/home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/dev/"
                        sh "scp -r ./src ${sshCopy}:/home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/dev/"
                        sh "scp -r ./startups/ ${sshCopy}:/home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/"
                        //Start Application
                        sh "ssh ${sshCopy} '/bin/bash /home/${CUSTOMER_NAME.toLowerCase()}/${PROJECT_NAME.toLowerCase()}/startups/start.sh dev ${CUSTOMER_NAME.toLowerCase()}'"
                    
                      }
                     }
                  }
                }
              }
            }
          }
        }
    }