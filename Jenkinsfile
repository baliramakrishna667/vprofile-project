pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'MAVEN9'
    }
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin1234'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.92.9'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-gropu'
        NEXUS_LOGIN = 'nexuslogin'

    }
    stages {
        stage("build artifact"){
            steps{
                sh "mvn -s settings.xml -DskipTests install"
            }
            post{
                success{
                    echo "now archiving......"
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
}