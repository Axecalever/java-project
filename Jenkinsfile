pipeline {
	agent none
	
	stages {
			stage('Unit Test') {
				agent {label 'centos_slave'}				
				steps {				
					sh 'ant -f test.xml -v'
					junit 'reports/result.xml' 				
				}		
			}
			stage('build') {
				agent {label 'centos_slave'}				
				steps {				
					sh 'ant -f build.xml -v'
				}		
			}
			
			stage('deploy') {
				agent {label 'centos_slave'}				
				steps {				
					sh 'mkdir /var/www/html/rectangles/all/${BRANCH_NAME}'
					sh 'cp dist/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/all/${BRANCH_NAME}'
				}		
			}		
			
			stage('Running on Centos') {
				agent {label 'centos_slave'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/${BRANCH_NAME}/rectangle_${BUILD_NUMBER}.jar'
				}		
			}
			
			stage('Test on Debian') {
				agent {docker 'openjdk:8u121-jre'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/${BRANCH_NAME}/rectangle_${BUILD_NUMBER}.jar'
					sh 'java -jar rectangle_${BUILD_NUMBER}.jar 3 4'
				}		
			}
			
			stage('Promote to Green') {
				agent {label 'centos_slave'}
				when {
					branch 'master'
				}
				steps {
					sh 'cp /var/www/html/rectangles/all/${BRANCH_NAME}/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/green/ '
				}
			}
			stage('Promote from Development Branch to Master') {
				agent {label 'centos_slave'}
				when {
					branch 'development'
				}
				steps {
					echo 'Stashing Any Local Changes'
					sh 'git stash'
					echo "Checking out Development Branch"
					sh 'git checkout development'
					echo "Checking out Master Branch"
					sh 'git checkout Development'
					echo 'Merging development into Master Branch'
					sh 'git merge development'	
					sh 'git push origin master '
				}
			}
	}	
}		
