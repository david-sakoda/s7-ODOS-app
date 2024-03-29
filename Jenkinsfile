#!/bin/bash
pipeline {
    agent any 
	options {
    buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '3'))
	}
    environment {
        def notificationContacts = "${distro}"
		def appName = "ODOS"
		def baseURL = ""
		def appURL = "${baseURL}/${environment}/${appName}"
		def environment = "DEV"
		def emailBodyPost = ""
		def fortifySh = ""	
    }

    stages {
        stage ('Initialize') {
            steps {
				script{
					try {
						sh 'echo "PATH = ${PATH}"'
						sh 'echo "M2_HOME = ${M2_HOME}"'
						sh 'echo "ehlo"'
					}
					catch (Exception e) {
						error "Stage $env.STAGE_NAME Failed"
					}
				}
            }
        }
		
		stage ('Start Email') {
			steps {
				script{
					try {
						echo "send an email"
						mail to:"${notificationContacts}",
						mimeType: "text/html",
						subject:"STARTED: ${currentBuild.fullDisplayName}",
						body: "<font color=\"blue\"><strong>STARTED</strong></font>: Build started" 
					}
					catch (Exception e) {
						error "Stage $env.STAGE_NAME Failed"
					}
				}
			}
		}
		
		stage ('Build') {
			steps {
				script{
					try {
						//checkout code
						//checkout([$class: 'GitSCM', branches: [[name: '*/david-test']], extensions: [], userRemoteConfigs: [[credentialsId: 'gitAuth', url: 'https://github.com/david-sakoda/s7-ODOS-app.git']]])
						dir('./keycloak'){
							sh 'docker build -t asonadmin/odos_keycloak:$BUILD_ID --no-cache .'
						}
					}
					catch (Exception e) {
						error "Stage $env.STAGE_NAME Failed"
					}
				}
			}
		}
		
		stage ('Security Scan') {
            steps {
				script{
					try {
						
						echo "security scan"
					}
					catch (Exception e) {
						error "Stage $env.STAGE_NAME Failed"
					}
				}
            }
        }
		
		stage ('Deploy') {
            steps {
				script{
					try {
					  echo "commented for now"
					  //sh 'kubectl set image deployment/odos/keycloak odos/keycloak=odos/keycloak:$BUILD_ID'
					  //sh 'ssh jenkins@172.20.255.1 kubectl apply -f /path/keycloak-deployment.yml --kubeconfig=keycloak-deployment.yml'
					   //sh 'kubectl get all'
					   //sh 'ssh jenkins@172.20.255.1 kubectl set image deployment/odos_keycloak  --namespace=odos"
					   
					}
					catch (Exception e) {

						error "Stage $env.STAGE_NAME Failed"
					}
				}
            }

        }		
		
		stage('Store Image') {
	    	steps {
				sh 'docker tag asonadmin/odos_keycloak:$BUILD_ID asonadmin/odos_keycloak:latest'
				withCredentials([usernamePassword(credentialsId: 'dockerHubAuth', passwordVariable: 'pass', usernameVariable: 'user')]) {
					sh 'docker login -u "' + user + '"' + ' -p "' + pass + '" docker.io'
				}
		      	sh 'docker push asonadmin/odos_keycloak:$BUILD_ID'
				sh 'docker push asonadmin/odos_keycloak:latest'
	      	}
		}
		
		stage ('Automation Tests') {
            steps {
				script{
					try {
						sh 'echo "Starting Automation Tests. this is a place holder"'
					}
					catch (Exception e) {
						error "Stage $env.STAGE_NAME Failed"
					}
				}
            }
        }
	}
	post{
		always{
			script{
				echo "always"
			}
		}
		success {
            mail to:"${notificationContacts}",
			mimeType: "text/html",
			subject:"SUCCESS: ${currentBuild.fullDisplayName}",
 		   body: "<font color=\"green\"><strong>SUCCESS</strong></font>: SUCCESS <p> "
		   echo "success"
		}
		failure {
			mail to:"${notificationContacts}",
			mimeType: "text/html",
			subject:"FAILURE: ${currentBuild.fullDisplayName}",
		    body: "<font color=\"red\"><strong>FAILED</strong></font>: FAILURE <p> try again"
			echo "fail"
		}
	}
}