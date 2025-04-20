pipeline{
    agent any
    tools{
        jdk "JDK17"
        maven "MAVEN3.9"
    }
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER ='admin'
        NEXUS_PASS = 'admin1234'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.23.185'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-gropu'
        NEXUS_LOGIN = 'nexuslogin'
    }
    stages{
        stage("build"){
            steps{
                sh 'mvn -s settings.xml -DskipTexts install'
            }
        }
         post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
    }
    stage("test"){
        steps{
            sh 'mvn test'
        }
    }
    stage("sheck-style-anlysis') {
          steps{
              sh 'mvn checkstyle:checkstyle'
          }
          }
          steps {
            withSonarQubeEnv('sonar-pro') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

          
}
