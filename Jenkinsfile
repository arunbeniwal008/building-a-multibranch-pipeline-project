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
                withAWS(region:"ap-south-1", credentials:"AWS-ARUN") {
                    s3Upload(file:"build", bucket:"aruns3jenkins", path:"${env.BRANCH_NAME}")
                }    
            }
        }
    }
    post {
        always {
            emailext attachLog: true, body: 'Extended email', subject: 'Extended email', to: 'arunbeniwal2010@gmail.com'
            mail(subject: 'Production Build', body: 'New Deployment to Production', to: 'arunbeniwal2010@gmail.com')
            sh "chmod -R 777 ."
            deleteDir() 
        }
    }
}
