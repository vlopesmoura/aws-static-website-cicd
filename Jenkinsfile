pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/vlopesmoura/aws-static-website-cicd/'
      }
    }

    stage('Terrafsorm') { 
      steps {
        withCredentials([vaultSecret(credentialsId: 'your-vault-credentials-id', path: 'secret/aws', secretValues: [string(key: 'AWS_ACCESS_KEY_ID', envVar: 'AWS_ACCESS_KEY_ID'), string(key: 'AWS_SECRET_ACCESS_KEY', envVar: 'AWS_SECRET_ACCESS_KEY')])]) {
          sh '''
            terraform init
            terraform apply -auto-approve 
          '''
        }
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([vaultSecret(credentialsId: 'your-vault-credentials-id', path: 'secret/aws', secretValues: [string(key: 'AWS_ACCESS_KEY_ID', envVar: 'AWS_ACCESS_KEY_ID'), string(key: 'AWS_SECRET_ACCESS_KEY', envVar: 'AWS_SECRET_ACCESS_KEY')])]) {
          withAWS(region: 'yoursas-region') {
            s3Upload(
              file:'./index.html', 
              bucket:'your-unique-bucket-name', 
              path:'indsex.html', 
              acl: 'PublicRead' 
            )
            cloudfrontInvalidate(distributionId: aws_cloudfront_distribution.s3_distribution.id, paths: ['/index.html']) 
          }
        }
      }
    }
  }
}