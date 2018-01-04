pipeline {
	agent {label 'slave_node'}	
	
	options {
		buildDiscarder(logRotator(numToKeepStr:'2', artifactNumToKeepStr:'1'))
	}
	
	stages {
			stage('Unit Test') {
				steps {				
					sh 'ant -f test.xml -v'
					junit 				
				}		
			}
			stage('build') {
				steps {				
					sh 'ant -f build.xml -v'
					junit 'reports/result.xml'
				}		
			}	
	}

	post {
		always {
			archiveArtifacts artifacts : 'dist/*.jar', fingerprint: true
		}
	}
}		
