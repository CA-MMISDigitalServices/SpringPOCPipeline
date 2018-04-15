pipeline {
    agent any
    environment { 
        mvnHome = tool 'Maven_Config' 
    }
    stages {
    	stage('Preparation') {
			steps {
				git url: 'https://github.com/CA-MMISDigitalServices/Dev.git', branch: 'master'
			}
		}
        stage('Build') {
            steps {
				sh "'${mvnHome}/bin/mvn' -X -B --file /var/lib/jenkins/workspace/TestPipeline/SpringPOC -Dmaven.test.failure.ignore clean install cobertura:cobertura -Dcobertura.report.format=xml"
            }
            post {
                always {
                    echo '******************* always'
					jiraIssueSelector(issueSelector: [$class: 'DefaultIssueSelector'])
                }
				failure {
					echo '****************** failure'
					jiraComment body: 'Build Failed', issueKey: 'PTP'
				}
				success {
					echo '****************** success'
					jiraComment body: 'Build Succsessful', issueKey: 'PTP-26'
					script {
						testIssue = [fields: [ project: [key: 'RFC'],
									summary: 'New JIRA Created from Jenkins.',
									description: 'New JIRA Created from Jenkins.',
									issuetype: [id: '3']]]

						response = jiraNewIssue issue: testIssue, site: 'CAMMMIS'

						echo response.successful.toString()
						echo response.data.toString()
					}
				}	
			}
        }
        stage('robot test') {
        	steps {
        		node('master') {
  				 	script {
                    	MYLIST = []
                    	MYLIST += "param-one"
                    	MYLIST += "param-two"
                    	MYLIST += "param-three"
                    	MYLIST += "param-four"
                    	MYLIST += "param-five"
                    
                   		 MRSTR = jiraJqlSearch jql: 'PROJECT = PTP', auditLog: true, site: 'CAMMIS'
                    
//                    	echo MRSTR.data.toString()
	
						for (def element = 0; element < MYLIST.size(); element++) {
				
							echo MYLIST[element]  
                        
               			}
				 	}
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
		stage('Code Coverage Report') {   
            steps {
				cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
	    	}
			post {
                always {
                    echo 'Code Coverage Report  Done'
                }
				failure {
					echo 'Code Coverage Report  failure'
				}
				success {
					echo 'Code Coverage Report Success'
				}	
			}
		}
//		stage('Nexus Snapshot Upload') {   
//            steps {
//				nexusArtifactUploader artifacts: [[artifactId: 'SpringPOC', classifier: '', file: '/var/lib/jenkins/workspace/TestPipeline/SpringPOC/target/springpoc-1.0.0-BUILD-SNAPSHOT.war', type: 'war']], credentialsId: 'Admin', groupId: 'CA-MMIS.jenkins.ci.SpringPOC', nexusUrl: '158.96.16.218:8081', nexusVersion: 'nexus2', protocol: 'http', repository: 'http://158.96.16.218:8081/nexus/content/repositories/snapshots/', version: '${BUILD_NUMBER}' 
//	    	    nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: 'debug', extension: '', filePath: '/var/lib/jenkins/workspace/TestPipeline/SpringPOC/target/springpoc-1.0.0-BUILD-SNAPSHOT.war']], mavenCoordinate: [artifactId: 'SpringPOC', groupId: 'CA-MMIS.jenkins.ci.SpringPOC', packaging: 'war', version: '${BUILD_NUMBER}']]]
//			}
//			post {
//                always {
//                   echo 'Nexus Snapshot Upload  Done'
//                }
//				failure {
//					echo 'Nexus Snapshot Upload  failure'
//				}
//				success {
//					echo 'Nexus Snapshot Upload Success'
//				}	
//			}
//		}
 	}
 }
