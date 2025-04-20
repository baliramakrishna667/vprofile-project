pipeline{
    agent any
    tools{
        jdk "JDK17"
        maven "MAVEN3.9"
    }
    environment {
        SNAP-REPO = 'vprofile-snapshot'
        NEXUS-USER ='admin'
        NEXUS-PASS = 'admin1234'
        RELEASE-REPO = 'vprofile-release'
        CENTRAL-REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.23.185'
        NEXUSPORT = '8081'
        NEXUS-GRP-REPO = 'vpro-maven-gropu'
        NEXUS-LOGIN = 'nexuslogin'
    }
    stages{
        stage("build"){
            steps{
                sh 'mvn -s settings.xml -DskipTexts install'
            }
        }
    }
}
