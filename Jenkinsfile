pipeline {
    agent any
    environment {
              APP_NAME = "register-app-pipeline"
              IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
              GIT_REPO_URL = 'https://github.com/harasindhu/gitops-register-app'
              GIT_BRANCH = 'main'
              GIT_CREDENTIALS_ID = 'github'
    }
    stages {
        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/harasindhu/gitops-register-app'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                 // Clone the repository
                    checkout([$class: 'GitSCM',
                              branches: [[name: "${main}"]],
                              userRemoteConfigs: [[url: "${https://github.com/harasindhu/gitops-register-app}"]],
                              extensions: [[$class: 'CleanCheckout'],
                                           [$class: 'CloneOption', shallow: true]],
                              doGenerateSubmoduleConfigurations: false,
                              submoduleCfg: [],
                              browser: [$class: 'AutoBrowser', repoUrl: "${https://github.com/harasindhu/gitops-register-app}.git"],
                              credentialsId: "${github}"])

                    // Modify deployment files or any other necessary changes
                    sed -i 's/old-image/new-image/g' deployment.yaml

                    // Commit and push the changes
                    sh 'git add .'
                    sh 'git commit -m "Update deployment"'
                    sh 'git push origin ${main}'
                }
            }
        }
      
    }
}
