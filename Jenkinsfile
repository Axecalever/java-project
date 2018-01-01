pipeline {
	agent 'centos_slave'	

	stages {
			stage('build') {
				steps {				
					sh 'ant -f build.xml -v'
				}		
			}

	}
}
