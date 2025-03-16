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
        NEXUS_IP = '172.31.33.101'
        NEXUS_PORT = '8081'
        NEXUS_URL = "http://${NEXUS_IP}:${NEXUS_PORT}/repository"
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
                sh 'mvn -s setting.xml -DskipTests install'
            }
        }
    }
}
