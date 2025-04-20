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
        NEXUS_GRP_REPO = 'vpro-maven-gropu'
        NEXUS_LOGIN    = 'nexuslogin'
    }

  satges{
      stage("build"){
          steps{
              sh 'mvn -s settings.xml -DskipTest install'
          }
      }
  }
}
