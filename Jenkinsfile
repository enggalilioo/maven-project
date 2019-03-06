pipeline {
    agent none
    stages {
	
	stage('Git-Clone on Master') {
	    agent {
                        label "master"
                }
        steps {
                echo 'This will clone source code on master agent'
                git 'https://enggalilioo@bitbucket.org/maven_sample/maven_sample.git'
                }
        }
        
    stage('Build on Master Agent') {
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

        

	
    stage('Run Tests') {
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