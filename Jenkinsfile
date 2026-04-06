pipeline{
    agent any
    environment{
        AWS_REGION = "eu-north-1"
        S3_BUCKET = "demo-frontend11"
        CLOUDFRONT_DISTRIBUTION_ID = "E2F37JR1TTEERB"
    }

    stages {
        stage('checkout') {
            steps {
                git branch: 'Greeshma',
                credentialsId: 'git-cred',
                url: 'https://github.com/greeshmakiaq/demo-frontend.git'
            }
        }

        stage('check node version'){
            steps{
                script{
                    sh '''npm -v
                    node -v'''
                }
            }
        }

        stage('build stage'){
            steps{
                script{
                    sh '''npm install --legacy-peer-deps
                    npm run build'''
                }
            }
        }

        stage('verify build output'){
            steps{
                script{
                    sh 'ls -la dist'
                }
            }
        }

        stage('deploy to s3'){
            steps{
                withAWS(credentials: 'aws-my-acc', region: "${AWS_REGION}") {
                    sh "aws s3 sync dist s3://${S3_BUCKET}/ --delete"
                }
            }
        }

        stage('cloudfront invalidation'){
            steps{
                withAWS(credentials: 'aws-my-acc', region: "${AWS_REGION}"){
                    sh "aws cloudfront create-invalidation \
                        --distribution-id ${CLOUDFRONT_DISTRIBUTION_ID} \
                        --paths '/*' "
                }
            }
        }
    }
    post{
        success{
            echo "frontend deployed"
        }

        failure{
            echo "frontend failed"
        }
    }
}
