pipeline {
    
	agent any

	tools {
        maven "MAVEN3.9"
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
        SONARSERVER='sonarserver'
        SONARSCANNER='sonarscanner'
    }
	
    
    stages{
               
        stage('Build'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post{
                success{
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test'){
            steps{
                sh 'mvn -s settings.xml test'
            }
        }

        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        stage('SonarQube Analysis'){
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps{
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.exclusions="**/*.java" \
                    -Dsonar.projectKey="vprofile-project" \
                    -Dsonar.projectName="vprofile-project" \
                    -Dsonar.projectVersion="1.0" \
                    -Dsonar.sources="src/" \
                    -Dsonar.java.binaries="target/test-classes/com/visualpathit/account/controllerTest/" \
                    -Dsonar.unitTest.reportPaths="target/surefire-reports/" \
                    -Dsonar.jacoco.reportPaths="target/jacoco.exec" \
                    -Dsonar.java.checkstyle.reportPaths="target/checkstyle-result.xml"
                    '''
                }

             }
        }

        stage('Quality Gate'){
            steps{
                // Reuse the Quality Gate status from the SonarQube analysis
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether the build should be failed if the Quality Gate fails
                    waitForQualityGate abortPipeline: true
                }
            }
        }

       
    // stage('Upload artefacts'){ //start of upload artefacts
    //         steps{
    //             script{
    //                 sh 'ls -l'
    //                 // use pipeline step plugin to find the war file
    //                 // Find the war file returned by the build
    //                 def artifact = findFiles(glob: '**/*.war')
    //                 // Get the path and name of the artifact
    //                 def artifactPath = artifact[0].path
    //                 // Get the name of the artifact
    //                 def artifactName = artifact[0].name

    //                 echo "Artifact Path: ${artifactPath}"
    //                 echo "Artifact Name: ${artifactName}"
    //                 // Upload the artifact to Nexus
    //                 sh "curl -v -u ${NEXUS_USER}:${NEXUS_PASS} --upload-file ${artifactPath} ${NEXUS_URL}/nexus/${RELEASE_REPO}/${artifactName}"
    //             }
    //         }
    //     } //end of upload artefacts

    stage('Upload artefacts'){
        steps{
            script{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${ENV.BUILD_NUMBER}-${ENV.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: "vprofile",
                        classifier: '',
                        file: 'target/vprofile.war',
                        type: 'war']
                    ]
            )
            }
        }
    
    }
  }
}