/* import shared library */
@Library('cicd-shared-library')_

pipeline {
    agent none
    stages {

        stage('Sonarqube') {
        agent any
         environment {
               scannerHome = tool 'sonar_scanner'
            }
         steps {
            withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
                sh"${scannerHome}/bin/sonar-scanner"
            }
         }
       }

        stage('Prepare ansible environment') {
            agent any
            environment {
                VAULTKEY = credentials('vaultkey')
                DEVOPSKEY = credentials('devopkey')
            }
            steps {
                sh 'echo \$VAULTKEY > vault.key'
                sh 'cp \$DEVOPSKEY id_rsa'
                sh 'chmod 600 id_rsa'
            }
        }
        stage('Test and deploy the application in preproduction') {
             agent { docker { image 'registry.gitlab.com/robconnolly/docker-ansible:latest' } }
            stages {
               stage("Install ansible role dependencies") {
                   steps {
                       sh 'ansible-galaxy install -r roles/requirements.yml'
                   }
               }
               stage("Ping targeted hosts") {
                   steps {
                       sh 'ansible all -m ping -i hosts --private-key id_rsa'
                   }
               }
               stage("Build docker images on build host") {
                   when {
                      expression { GIT_BRANCH == 'origin/dev' }
                   }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "build" --limit build battleboat.yml'
                   }
                }

               stage("Scan docker images on build host") {
                   when {
                      expression { GIT_BRANCH == 'origin/dev' }
                   }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --limit build  clair-scan.yml'
                   }
                }
               stage("Push on Artifactory registry") {
                   when {
                      expression { GIT_BRANCH == 'origin/dev' }
                   }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "push" --limit push battleboat.yml'
                   }
                }
               stage("Deploy application in preproduction") {
                  when {
                      expression { GIT_BRANCH == 'origin/dev' }
                  }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "preprod" --limit preprod battleboat.yml'
                   }
                }
               stage("Ensure application is deployed in preproduction") {
                  when {
                      expression { GIT_BRANCH == 'origin/dev' }
                  }
                  steps {
                      sh 'ansible-playbook  -i hosts --vault-password-file vault.key --tags "preprod" check_deploy_app.yml'
                  }
                } 
            }
        }
        stage('Test and deploy the application in production') {
            agent { docker { image 'registry.gitlab.com/robconnolly/docker-ansible:latest' } }
            stages {
               stage("Install ansible role dependencies") {
                  
                   when {
                      expression { GIT_BRANCH == 'origin/main' }
                   }
                   steps {
                       sh 'ansible-galaxy install -r roles/requirements.yml'
                   }
               }
               stage("Ping targeted hosts") {
                   when {
                      expression { GIT_BRANCH == 'origin/main' }
                   }
                   steps {
                       sh 'ansible all -m ping -i hosts --private-key id_rsa'
                   }
               }

               stage("Deploy application in production") {
                   when {
                      expression { GIT_BRANCH == 'origin/main' }
                  }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "prod" --limit prod battleboat.yml'
                   }
               }
               stage("Ensure application is deployed in production") {
                  when {
                      expression { GIT_BRANCH == 'origin/main' }
                  }
                  steps {
                      sh 'ansible-playbook  -i hosts --vault-password-file vault.key --tags "prod" check_app.yml'
                  }
               }
            }
        }
    }
    
    post {
    always {
       script {
         clean
         slackNotifier currentBuild.result
     }
    }
    }  
}

            

    
