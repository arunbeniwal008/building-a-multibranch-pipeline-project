pipeline
{
    agent any
    environment {
        CI = false
    }
    stages{
        stage('build')
        {
            steps{
                //sh "echo ott#4u@pmsl | sudo -S bundle install" 
                sh "npm install"
                sh "rm -rf `find -type d -name .git`"
                sh "npm run build --env ${env.BRANCH_NAME}"
                //sh "npm run build:${env.BRANCH_NAME}"
            }
        }
        stage("Upload") {
            steps {
                withAWS(region:"ap-southeast-1", credentials:"AWS-Planetcast") {
                    s3Upload(file:"build", bucket:"stg-web.planetcast.in", path:"${env.BRANCH_NAME}")
                }    

            }
        }
    }
    post {
        always {
            // delete the workspace
            sh "chmod -R 777 ."
            deleteDir() 
        }
    }
}
