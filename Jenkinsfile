def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    
    environment {
        registryCredential = 'ecr:ap-southeast-1:awscreds'
        appRegistry = '842485143083.dkr.ecr.ap-southeast-1.amazonaws.com/javaspringpoc'
        awsRegistry = "842485143083.dkr.ecr.ap-southeast-1.amazonaws.com/javaspringpoc"
        cluster = "Stage"
        service = "service-stage"
    }
    
    stages {
        stage('Initialize'){
            steps {
                script {
                    def dockerHome = tool 'mydocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                    sh 'sudo systemctl start docker'
                    
                    // Add Jenkins user to the docker group
                    sh 'sudo usermod -aG docker jenkins'
                    
                    // Grant permissions to Docker socket
                    sh 'sudo chmod 666 /var/run/docker.sock'
                }
            }
        }
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
                 withAWS(credentials: 'awscreds', region: 'ap-southeast-1') {
                     sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                 } 
             }
         }
        
    }
}
