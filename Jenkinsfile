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
        CENTRAL_REPO = 'vprofile-central'
        NEXUSIP = '172.31.92.9'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vprofile-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER= 'sonarserver'
        SONARSCANNER= 'sonarscanner'

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
        stage("unit test") {
            steps{
                sh "mvn -s settings.xml test"
            }
        }
        stage("checkstyle analysis") {
            steps{
                sh "mvn -s settings.xml checkstyle:checkstyle"
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
           environment {
              scannerHome = tool "${SONARSCANNER}"
           }
           steps {
              withSonarQubeEnv("${SONARSERVER}") {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
               -Dsonar.projectName=vprofile \
               -Dsonar.projectVersion=1.0 \
               -Dsonar.sources=src/ \
               -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
               -Dsonar.junit.reportsPath=target/surefire-reports/ \
               -Dsonar.jacoco.reportsPath=target/jacoco.exec \
               -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
               }
            }
        }
    } 
    
}