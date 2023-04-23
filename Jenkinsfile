pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:AKIA2VF2CCJZ2RTI3OY3'
       appRegistry = "732682130035.dkr.ecr.ap-northeast-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://732682130035.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "Capstone"
        service = "Capstone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/jimit-exe/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'Capstone', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
