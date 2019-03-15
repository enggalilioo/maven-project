pipeline {
    agent none
    stages {
	
	stage('Clean Current Workspace') {
	    agent {
                        label "master"
                }
        steps {
            deleteDir()
                }
    }
    
    stage('Clone App source code on Build Agent') {
	    agent {
                        label "master"
                }
        steps {
                // Git clone from bitbucket.org 
                echo 'This will clone source code on master node'
                git 'https://github.com/enggalilioo/maven-project.git'
                bat 'git checkout development'
                }
    }
        
    stage('build && SonarQube analysis') {
        agent {
                        label "master"
                }
        steps {
                withSonarQubeEnv('SonarQube7.6') {
                        bat 'mvn clean package sonar:sonar'
                        archiveArtifacts artifacts: 'single-module\\target\\*.jar', onlyIfSuccessful: true
                }
            }
        post {
            always {
                junit 'single-module/target/surefire-reports/*.xml'
            }
        }
    }

    stage("SonarQube Quality Gate") {
        agent {
                        label "master"
                }
        steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
    }   

	
    stage('Copy && deploy App to Dev Env.') {
                    agent {
                        label "Windows_Node"
                    }
                    steps {
                        echo "Copy Artifact from the last successful build"
                        copyArtifacts filter: 'single-module/target/*.jar', fingerprintArtifacts: true, projectName: '${JOB_NAME}', selector: specific('${BUILD_NUMBER}')
                        }
    }

    }
 }