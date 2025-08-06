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
                sshagent(credentials:['robot']) {
					// copy of the war file to tomcat webapps
					sh "scp -o StrictHostKeyChecking=no target/*.war ec2-user@54.156.90.238:/opt/tomcat8/webapps/wiproproject.war"
					// stop and start tomcat server
					sh "ssh ec2-user@54.156.90.238 /opt/tomcat8/bin/shutdown.sh"
					sh "ssh ec2-user@54.156.90.238 /opt/tomcat8/bin/startup.sh"
				}
            }
        }
    }
}
