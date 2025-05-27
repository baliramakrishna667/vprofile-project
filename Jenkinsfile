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
        SONARSERVER      = 'sonarserver'     // Must match Jenkins > Configure System
        SONARSCANNER     = 'sonarscanner'    // Must match Jenkins > Global Tool Config
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
                        echo "Sonar Scanner Version:"
                        ${scannerHome}/bin/sonar-scanner --version

                        echo "Listing build outputs..."
                        ls -l target/
                        ls -l target/surefire-reports || true
                        ls -l target/checkstyle-result.xml || true

                        echo "Running SonarQube scan..."
                        ${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=vprofile \
                          -Dsonar.projectName=vprofile \
                          -Dsonar.projectVersion=1.0 \
                          -Dsonar.sources=src \
                          -Dsonar.java.binaries=target/classes \
                          -Dsonar.junit.reportsPath=target/surefire-reports \
                          -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml \
                          -Dsonar.working.directory=.scannerwork \
                          -X
                    '''
                }
            }
        }
    }
}
