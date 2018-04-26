pipeline {
    agent any
    environment { 
        mvnHome = tool 'Maven_Config' 
    }
    stages {
    	stage('Preparation') {
			steps {
				git url: 'https://github.com/CA-MMISDigitalServices/Dev.git', branch: 'errorTest'
			}
		} 
		stage('Starting Build') {
            steps {
				slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
			}
		} 
       stage('Build') {
            steps {
				script {
//					input message: 'Approve deployment?'
					sh "'${mvnHome}/bin/mvn' -X -B --file /var/lib/jenkins/workspace/TestPipeline/SpringPOC -Dmaven.test.failure.ignore clean install cobertura:cobertura -Dcobertura.report.format=xml"
				}
            }
            post {
                always {
                    echo 'Build Stage always'

                }
				failure {
					echo 'Build Stage failure'
					script {
						testIssue = [fields: [ project: [key: 'PTP'],
									summary: 'Jenkins Build Failure.',
									description: "Jenkins Build Failure -  Job name: '${env.JOB_NAME} - Build Number: ${env.BUILD_NUMBER}  URL: ${env.BUILD_URL}'",
									priority: [name: 'Highest'],
									issuetype: [name: 'Bug']]]

						response = jiraNewIssue issue: testIssue, site: 'CAMMIS'

						echo response.successful.toString()
						echo response.data.toString()
						
						slackSend (color: '#FFFF00', message: "Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

					} 
				}
				success {
					echo 'Build Stage Success'
					slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
				}	
			} 
        } 
		
		stage('SonarQube analysis') { 
    		steps { 
				withSonarQubeEnv('SonarQubeServer') {
					sh '/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/Sonar/bin/sonar-scanner' +
					' -Dsonar.host.url=http://158.96.16.211:9000/' + 
					' -Dsonar.projectVersion=1.0' +
					' -Dsonar.sourceEncoding=UTF-8' +
					' -Dsonar.projectKey=TestPipeline' +
					' -Dsonar.java.binaries=/var/lib/jenkins/workspace/TestPipeline/SpringPOC/target/classes' +
					' -Dsonar.sources=SpringPOC/src' +
					' -Dsonar.projectBaseDir=/var/lib/jenkins/workspace/TestPipeline'
				}
			}
			post {
                always {
                    echo 'SonarQube Analysis  Done'
                }
				failure {
					echo 'SonarQube Analysis  failure'
					script {
						echo 'AWS Code Deploy  failure'
						testIssue = [fields: [ project: [key: 'PTP'],
									summary: 'Jenkins Build Failure.',
									description: "Jenkins Build Failed - SonarQube Analysis Failed -  Job name: '${env.JOB_NAME} - Build Number: ${env.BUILD_NUMBER}  URL: ${env.BUILD_URL}'",
									priority: [name: 'Highest'],
									issuetype: [name: 'Bug']]]

						response = jiraNewIssue issue: testIssue, site: 'CAMMIS'

						echo response.successful.toString()
						echo response.data.toString()
						
						slackSend (color: '#FFFF00', message: "Failed: Job - SonarQube Analysis Failed '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
								// Fail the build
//						error "Pipeline aborted due to quality gate failure "
					}
				}
				success {
					echo 'SonarQube Analysis Success'
				}	
			}
		} 
    	stage('SonarQube Quality Gate') { 
			steps {
				node('master'){ 
					script {
						timeout(time: 1, unit: 'HOURS') { 
							echo '************ Inside Quality Gate'
							qualityGate = waitForQualityGate() 
							echo qualityGate.status.toString() 
							if (qualityGate.status != 'OK') {
							
								testIssue = [fields: [ project: [key: 'PTP'],
									summary: 'Jenkins Build Failure.',
									description: "Jenkins Build Sonar Quality Gate Failure -  Job name: '${env.JOB_NAME} - Build Number: ${env.BUILD_NUMBER}  URL: ${env.BUILD_URL}'",
									priority: [name: 'Highest'],
									issuetype: [name: 'Bug']]]

								response = jiraNewIssue issue: testIssue, site: 'CAMMIS'

								echo response.successful.toString()
								echo response.data.toString()
						
								slackSend (color: '#FFFF00', message: "Failed: Job - Sonar Quality Gate Failure '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
								// Fail the build
								error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
							}
						}
					}
				}
			}
			post {
                always {
                    echo 'SonarQube Quality Gate  Done'
                }
				failure {
					echo 'SonarQube Quality Gate  failure'
				}
				success {
					echo 'SonarQube Quality Gate Success'
				}	
			}
		}
		stage('Unit Test Report') {   
            steps {
				junit '**/target/surefire-reports/*.xml'
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
