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
				sh "'${mvnHome}/bin/mvn' -X -B --file /var/lib/jenkins/workspace/TestPipeline/SpringPOC -Dmaven.test.failure.ignore clean install"
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
		}
    	stage('SonarQube Quality Gate') { 
			steps {
				node('master'){ 
					script {
						timeout(time: 1, unit: 'HOURS') { 
							echo '************ Inside Quality Gate'
							qualityGate = waitForQualityGate() 
							echo qualityGate.status.toString() 
//					if (qualityGate.status != 'OK') {
//						error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
//							}
						}
					}
				}
			}
		}
		stage('Test') {   
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
