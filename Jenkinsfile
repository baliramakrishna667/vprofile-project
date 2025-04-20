pipeline {
    agent any

    tools {
        jdk "JDK17"
        maven "MAVEN3.9"
    }

    environment {
        SNAP_REPO      = 'vprofile-snapshot'
        NEXUS_USER     = 'admin'
        NEXUS_PASS     = 'admin1234'
        RELEASE_REPO   = 'vprofile-release'
        CENTRAL_REPO   = 'vpro-maven-central'
        NEXUSIP        = '172.31.23.185'
        NEXUSPORT      = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN    = 'nexuslogin'
    }

    stages {
        stage('Generate settings.xml') {
            steps {
                script {
                    writeFile file: 'settings.xml', text: """
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">
    <servers>
        <server>
            <id>${env.SNAP_REPO}</id>
            <username>${env.NEXUS_USER}</username>
            <password>${env.NEXUS_PASS}</password>
        </server>
        <server>
            <id>${env.RELEASE_REPO}</id>
            <username>${env.NEXUS_USER}</username>
            <password>${env.NEXUS_PASS}</password>
        </server>
        <server>
            <id>${env.CENTRAL_REPO}</id>
            <username>${env.NEXUS_USER}</username>
            <password>${env.NEXUS_PASS}</password>
        </server>
        <server>
            <id>${env.NEXUS_GRP_REPO}</id>
            <username>${env.NEXUS_USER}</username>
            <password>${env.NEXUS_PASS}</password>
        </server>
    </servers>

    <mirrors>
        <mirror>
            <id>${env.CENTRAL_REPO}</id>
            <name>Nexus Mirror</name>
            <url>http://${env.NEXUSIP}:${env.NEXUSPORT}/repository/${env.NEXUS_GRP_REPO}/</url>
            <mirrorOf>*</mirrorOf>
        </mirror>
    </mirrors>
</settings>
"""
                }
            }
        }

        stage("Build") {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}
