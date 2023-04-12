pipeline {
    agent any
    triggers {
        pollSCM('* * * * *') // poll scm for every min
        }
        options {
            buildDiscarder(logRotator(numToKeepStr: '5'))
             timestamps()
            
        }
    stages {
        stage('hello'){
            agent any
            steps{
            echo 'hello'
            sh '''ls
                  pwd 
                  '''
            }
        }
        stage('devops'){
            agent any
            steps{
            echo 'devops'
            }
        }
        stage('git integration') {
            agent {
            node {
                label 'master'
                customWorkspace '/var/lib/jenkins/devops'
            }
            }
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-ssh-private-key', url: 'git@github.com:sravandevops15/devops-15-maven.git']])
            }
        }
        stage('maven-build'){
            tools{
                maven 'maven3'
            }
            steps{
                sh 'mvn clean package'
            }
        }
        stage('nexus upload'){
            steps{
                nexusArtifactUploader artifacts: [[
                    artifactId: 'my-app',
                    classifier: '',
                    file: 'target/my-app-1.0-SNAPSHOT.jar',
                    type: 'jar']],
                    credentialsId: 'jenkins-nexus-cred',
                    groupId: 'com.mycompany.app',
                    nexusUrl: '34.100.152.129:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'devops15-jenkins-pipeline',
                    version: '1.0-SNAPSHOT'
            }
        }
        stage('email-notification'){
            steps{
                emailext attachLog: true, body: 'This is a $BUILD_NUMBER Failure is occurred with job Name $JOB_NAME', subject: 'Devops15-Email-Jenkins-Build-Failure:$JOB_NAME-$BUILD_NUMBER', to: 'sravanbunty.mj@gmail.com'
            }
        }
    }
}
