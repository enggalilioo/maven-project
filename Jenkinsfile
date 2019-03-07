pipeline {
    agent none
    stages {
	
	stage('Checkout source code on Jenkins master node') {
	    agent {
                        label "master"
                }
        steps {
                // Git clone from bitbucket.org 
                echo 'This will clone source code on master node'
                git 'https://github.com/enggalilioo/maven-project.git'
                bat 'git Checkout development'
                }
        }
        
    stage('Build project on Jenkins master node') {
	    agent {
                        label "master"
                }
        steps {
                // Run the maven build
                bat'mvn clean test package'
                archiveArtifacts artifacts: 'single-module\\target\\*.jar', onlyIfSuccessful: true
                }
        post {
            always {
                junit 'single-module/target/surefire-reports/*.xml'
            }
        }
        }

        

	
    stage('Deploy and Test the project') {
            parallel {
                stage('Copy Artifact to Windows_Node Agent') {
                    agent {
                        label "Windows_Node"
                    }
                    steps {
                        echo "Copy Artifact from the last successful build"
                        deleteDir() /* clean up our workspace */
                        copyArtifacts filter: 'single-module/target/*.jar', fingerprintArtifacts: true, projectName: 'Simple_pipeline', selector: lastSuccessful()
                        }
                    }
                stage('Run Jar in Windows_Node Agent') {
                    agent {
                        label "Windows_Node"
                          }
                    steps {
						echo "Run jar in Windows_Node Agent"
						// bat 'java -jar .\\single-module\\target\\single-module-project.jar'
					    }
                    }
                }
            }
        }
    }