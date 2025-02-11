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
                sh "npm install"
                sh "npm fund"
                sh "npm audit fix"
                sh "rm -rf `find -type d -name .git`"
                sh "npm run build --env ${env.BRANCH_NAME}"
                //sh "npm run build:${env.BRANCH_NAME}"
            }
        }
        stage("Data On S3@arun") {
            steps {
                withAWS(region:"ap-south-1", credentials:"AWS-ARUN") {
                    s3Delete(bucket:"aruns3jenkins", path:"${env.BRANCH_NAME}")
                    s3Upload(file:"build", bucket:"aruns3jenkins", path:"${env.BRANCH_NAME}")
                }
            }
        }
        stage("Upload @planetcast") {
            steps {
                withAWS(region:"ap-southeast-1", credentials:"AWS-Planetcast") {
                   s3Upload(file:"build", bucket:"stg-web.planetcast.in", path:"${env.BRANCH_NAME}")
                }
                }    
            }
        stage("Delete & Upload") {
            steps {
            withAWS(region:'ap-south-1',credentials:'AWS-ARUN') {
              s3Upload(bucket: 'aruns3jenkins', workingDir:'build', includePathPattern:"${env.BRANCH_NAME}");
              }
           }
    post {
        always {
            emailext(attachLog: true, body: 'Jenkins Build ', subject: 'Extended email', to: 'jackson@planetc.net')
            mail(subject: 'Production Build', body: 'New CI-CD Deployment to Production', to: 'jackson@planetc.net')
            sh "chmod -R 777 ."
            deleteDir() 
        }
    }
  }
 }
}
