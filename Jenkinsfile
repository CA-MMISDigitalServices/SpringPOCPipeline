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
        stage('Test') {   
            steps {
//                junit '**/target/surefire-reports/*.xml', 'testDataPublishers': (['$class': 'JiraTestDataPublisher', 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': 'true', 'autoResolveIssue': 'false', 'autoUnlinkIssue': 'true'])
//		  junit allowEmptyResults: true, healthScaleFactor: 2.0, keepLongStdio: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers : 'JiraTestDataPublisher', 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': 'true', 'autoResolveIssue': 'false', 'autoUnlinkIssue': 'true'
//		  junit allowEmptyResults: true, testDataPublishers: [[$class: 'JiraTestDataPublisher']], 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': true, 'autoResolveIssue': false, 'autoUnlinkIssue': true, ]], testResults: '**/target/surefire-reports/*.xml'
//                  junit allowEmptyResults: true, testDataPublishers: [[$class: 'JiraTestDataPublisher', 'configs': [], 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': true,'autoResolveIssue': false, 'autoUnlinkIssue': true, ]], testResults: '**/target/surefire-reports/*.xml'
//		    junit allowEmptyResults: true, testDataPublishers: [[$class: 'JiraTestDataPublisher']], testResults: '**/target/surefire-reports/*.xml'
//		  junit testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': true, 'autoResolveIssue': false, 'autoUnlinkIssue': true, 'configs': []]]
//junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', 'configs': [], 'projectKey': 'PTP', 'issueType': 'Bug', 'autoRaiseIssue': true, 'autoResolveIssue': false, 'autoUnlinkIssue': true, ]]
//junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', configs: [], projectKey: 'PTP', issueType: 'Bug', autoRaiseIssue: true, autoResolveIssue: false, autoUnlinkIssue: true, ]]
// junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', configs: [[SelectableArrayFields: [], SelectableFields: [], StringArrayFields, [], StringFields: [], UserFields: []]], projectKey: 'PTP', issueType: 'Bug', autoRaiseIssue: true, autoResolveIssue: false, autoUnlinkIssue: true, ]]
//junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', configs: [], projectKey: 'PTP', issueType: 'Bug', autoRaiseIssue: true, autoResolveIssue: false, autoUnlinkIssue: true ]]	    													
	   junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml', testDataPublishers: [[$class: 'JiraTestDataPublisher', configs: [[$class: 'SelectableFields', fieldKey: ${TEST_NAME} , value: 'test']], projectKey: 'PTP', issueType: 'Bug', autoRaiseIssue: true, autoResolveIssue: false, autoUnlinkIssue: true, ]]

	      
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
