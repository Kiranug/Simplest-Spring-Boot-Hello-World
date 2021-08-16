import groovy.json.JsonSlurperClassic
//def projects = readJSON file: "${env.WORKSPACE}\\Projects.json".           

pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
               withCredentials([gitUsernamePassword(credentialsId: 'gitaccess', gitToolName: 'Default')]) {
               git 'https://github.com/Kiranug/Simplest-Spring-Boot-Hello-World.git'
                }
          }
        }
         stage('Build') {
            steps {
                echo 'Building..'
                      load "$WORKSPACE/.envvars/name-dev.groovy"
                      echo "${env.DB_URL}"
                      echo "${env.DB_URL2}"
                      sh 'mvn clean package deploy'
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
                docker build -t ${env.EXPRESS_IMAGE_NAME} .
                docker tag ${env.EXPRESS_IMAGE_NAME} ${env.GCR_REPO}/${env.EXPRESS_IMAGE_NAME}:${currentBuild.number}
                docker push ${env.GCR_REPO}/${env.EXPRESS_IMAGE_NAME}:${currentBuild.number}
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
