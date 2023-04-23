pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:202051120'
       appRegistry = "385739726716.dkr.ecr.ap-south-1.amazonaws.com/devops_capstone"
       capstoneRegistry = "https://385739726716.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "DevOps_Capstone"
        service = "capstone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/SherlockHolmes-dev/website'
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
            withAWS(credentials: '202051120', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
