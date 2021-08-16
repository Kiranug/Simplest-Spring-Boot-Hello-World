import groovy.json.JsonSlurperClassic
//def projects = readJSON file: "${env.WORKSPACE}\\Projects.json".           

pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
               withCredentials([gitUsernamePassword(credentialsId: 'gitaccess', gitToolName: 'Default')]) {
               git "${env.GITREPO}"
                }
          }
        }
         stage('Build') {
            steps {
                echo 'Building..'
                      load "$WORKSPACE/.envvars/name-dev.groovy"
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
                docker build -t ${env.EXPRESS_IMAGENAME} .
                docker tag ${env.EXPRESS_IMAGENAME} ${env.GCR_REPO}/${env.EXPRESS_IMAGENAME}:${currentBuild.number}
                docker push ${env.GCR_REPO}/${env.EXPRESS_IMAGENAME}:${currentBuild.number}
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
