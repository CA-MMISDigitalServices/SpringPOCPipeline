pipeline {
    agent any
	environment { 
        mvnHome = tool 'apache-maven-3.5.3'
    }
    stages {
		stage('Preparation') {
			steps {
				git url: 'https://github.com/CA-MMISDigitalServices/Dev.git', branch: 'master'
			}
		}
        stage('Build') {
            steps {
				sh "'${mvnHome}/bin/mvn' -X -B --file D:/Software/Install/jenkins/workspace/TestPipeline/SpringPOC/pom.xml -Dmaven.test.failure.ignore clean install"
                }
            post {
                always {
                    echo '******************* always'
                }
				failure {
					echo '****************** failure'
				}
				success {
					echo '****************** success'
				}
            }
        }
        stage('Test') { 
            steps {
//                sh 'mvn test' 
                junit '**/target/surefire-reports/*.xml'
//                junit '\\target\\surefire-reports/*.xml'

            }
            post {
                always {
                echo 'always'
                }
				changed {
					echo 'change'
				}
				aborted {
					echo 'aborted'
				}
				failure {
					echo 'failure'
				}
				success {
					echo 'success'
				}
				unstable {
					echo 'unstable'
				}
            }
        }
    }
}