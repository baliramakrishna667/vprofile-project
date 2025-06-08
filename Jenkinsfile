pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVEN3.9'
    }

    environment {
        SNAP_REPO        = 'vprofile-snapshot'
        NEXUS_USER       = 'admin'
        NEXUS_PASS       = 'admin1234'
        RELEASE_REPO     = 'vprofile-release'
        CENTRAL_REPO     = 'vprofile-central'
        NEXUSIP          = '172.31.6.178'
        NEXUSPORT        = '8081'
        NEXUS_GRP_REPO   = 'vprofile-group'
        NEXUS_LOGIN      = 'nexuslogin'
        SONARSERVER      = 'sonarserver'
        SONARSCANNER     = 'sonarscanner'
        awscredentional  = 'ecr:ap-south-1:awscreds'
        appregistery     = '381711065088.dkr.ecr.ap-south-1.amazonaws.com/vprofilerepo'
        vprofileregistry = 'https://381711065088.dkr.ecr.ap-south-1.amazonaws.com'
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
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
                SONAR_SCANNER_OPTS = "--add-opens java.base/java.lang=ALL-UNNAMED"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner \
                      -Dsonar.projectKey=vprofile \
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
        stage('Quality gates'){
            steps {
                timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
            }
         }
        }

        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }

        stage("build docker image") {
            steps{
                script {
                    dockerImage = docker.build(appregistery + ":$BUILD_NUMBER", "./" )
                }
            }
        }

        stage("upload image") {
            steps{
                script{
                    docker.withRegistry(vprofileregistry,awscredentional) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push("latest")
                    }
                }
            }
        }

    }
}