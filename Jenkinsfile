pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVEN3.9'
    }

    environment {
        cluster = 'vprofilestaging_123'
        service = 'vprofiletask-service-tjxyo69z'
    }

    stages {
        stage("upload image into container") {
            steps{
                withAWS(credentials: 'awscreds', region: 'ap-south-1'){
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }

    }
}