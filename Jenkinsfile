def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    
    environment {
        registryCredential = 'ecr:ap-southeast-1:awscreds'
        appRegistry = '842485143083.dkr.ecr.ap-southeast-1.amazonaws.com/ecr'
        awsRegistry = "842485143083.dkr.ecr.ap-southeast-1.amazonaws.com/javaspringpoc"
        cluster = "Stage"
        service = "service-stage"
    }

    stages {
        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Dockerfiles/App/")
                }
            }
        }
        
        stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( awsRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        
         stage('Deploy to ECS staging') {
             steps {
                 withAWS(credentials: 'awscreds', region: 'us-east-1') {
                     sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                 } 
             }
         }
        
    }
}
