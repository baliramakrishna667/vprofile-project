pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVEN9'
    }

    environment {
        SNAP_REPO        = 'vprofile-snapshot'
        NEXUS_USER       = 'admin'
        NEXUS_PASS       = 'admin1234'
        RELEASE_REPO     = 'vprofile-release'
        CENTRAL_REPO     = 'vprofile-central'
        NEXUSIP          = '172.31.92.9'
        NEXUSPORT        = '8081'
        NEXUS_GRP_REPO   = 'vprofile-group'
        NEXUS_LOGIN      = 'nexuslogin'
        SONARSERVER      = 'sonarserver'     // Must match the name in Jenkins → Configure System → SonarQube
        SONARSCANNER     = 'sonarscanner'    // Must match the name in Jenkins → Global Tool Configuration
    }

    stages {

        stage("Build Artifact") {
            steps {
                sh "mvn -s settings.xml -DskipTests install"
            }
            post {
                success {
                    echo "Archiving artifact..."
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage("Unit Test") {
            steps {
                sh "mvn -s settings.xml test"
            }
        }

        stage("Checkstyle Analysis") {
            steps {
                sh "mvn -s settings.xml checkstyle:checkstyle"
            }
        }

        stage("Sonar Analysis") {
            environment {
                scannerHome = tool name: "${SONARSCANNER}", type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''
                        ${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=vprofile \
                          -Dsonar.projectName=vprofile \
                          -Dsonar.projectVersion=1.0 \
                          -Dsonar.sources=src \
                          -Dsonar.java.binaries=target/classes \
                          -Dsonar.junit.reportsPath=target/surefire-reports \
                          -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    '''
                }
            }
        }
    }
}
