pipeline {
    
	agent any

	tools {
        maven "MAVEN3.8"
        jdk "JDK17"
    }

    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin123'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUSIP = '172.31.33.101'
        NEXUSPORT = '8081'
        NEXUS_URL = "http://${NEXUSIP}:${NEXUSPORT}/repository"
        NEXUS_LOGIN = 'nexuslogin'
    }
	
    
    stages{
        stage('Clone code'){
            steps{
                git credentialsId: 'gitlogin', url: 'https://github.com/mogueye87/vprofile-project.git', branch: 'jenkins-ci'
            }
        }       
        stage('Build'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}
