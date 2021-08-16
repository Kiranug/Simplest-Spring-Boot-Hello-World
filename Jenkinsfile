import groovy.json.JsonSlurperClassic
//def projects = readJSON file: "${env.WORKSPACE}\\Projects.json".           

pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
               git 'https://github.com/Kiranug/Simplest-Spring-Boot-Hello-World.git'
          }
        }
         stage('Build') {
            steps {
                echo 'Building..'
                      load "$JENKINS_HOME/.envvars/name-dev.groovy"
                      echo "${env.DB_URL}"
                      echo "${env.DB_URL2}"
                      sh 'mvn clean package'
            }
             post {
                 success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                 }
             }
        }
            
          stage('Docker Build') {
            steps {
                echo 'Docker image Building..'
                sh """
                gcloud auth print-access-token | docker login -u oauth2accesstoken --password-stdin https://gcr.io
                docker build -t helloapp .
                docker tag helloapp gcr.io/neural-mantra-303016/helloapp:${currentBuild.number}
                docker push gcr.io/neural-mantra-303016/helloapp:${currentBuild.number}
                """
                
            }
        }
        
        /* stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'Staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '/var/lib/jenkins/workspace/maven-git_pipeline/webapp/target/webapp.war',
                                        removePrefix: '/var/lib/jenkins/workspace/maven-git_pipeline/webapp/target/',
                                        remoteDirectory: '/usr/share/tomcat/webapps/',
                                        execCommand: 'sudo systemctl restart tomcat'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        } */
    }
}
