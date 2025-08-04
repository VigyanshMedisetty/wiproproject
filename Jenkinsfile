pipeline {
    agent any
    tools {
        maven 'maven3'
    }


    stages {
        stage('git repo') {
            steps {
                git branch: 'main', url: 'https://github.com/VigyanshMedisetty/wiproproject'
            }
        }
        stage('maven build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('nexus artifact') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'wiproproject', classifier: '', file: 'target/wiproproject.war', type: 'war']], 
                credentialsId: 'nexus3', 
                groupId: 'in.wipro', 
                nexusUrl: '172.31.89.247:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'wiproproject-snapshots', 
                version: '1.0-SNAPSHOT'
            }
        }
        stage('tomcat deploy') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'tomcat8-dev', 
                transfers: [sshTransfer(cleanRemote: false, excludes: '', 
                execCommand: '''/opt/tomcat8/bin/shutdown.sh /opt/tomcat8/bin/startup.sh''', 
                execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, 
                removePrefix: 'target/', sourceFiles: 'target/*.war')], 
                usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
