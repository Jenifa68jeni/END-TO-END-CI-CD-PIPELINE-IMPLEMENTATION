pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/suffixscope/maven-web-app.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '34.229.76.233:8081',
                    groupId: 'com.scopeindia',
                    version: '1.0',
                    repository: 'scopeindia-release-repository',
                    credentialsId: 'nexus-credentials',
                    artifacts: [[artifactId: 'maven-web-app', classifier: '', file: 'target/maven-web-app.war', type: 'war']]
                )
            }
        }

        stage('Tomcat Deployment') {
            steps {
                sshagent(['Tomcat-Server-Agent']) {
                    sh 'scp target/maven-web-app.war ec2-user@184.72.215.120:/opt/tomcat/webapps/'
                }
            }
        }
    }
}
