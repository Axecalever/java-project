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
					sh 'cp dist/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/all'
				}		
			}		
			
			stage('Running on Centos') {
				agent {label 'centos_slave'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/rectangle_${BUILD_NUMBER}.jar'
				}		
			}
			
			stage('Test on Debian') {
				agent {docker 'openjdk:8u121-jre'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/rectangle_${BUILD_NUMBER}.jar'
					sh 'java -jar rectangle_${BUILD_NUMBER}.jar 3 4'
				}		
			}
			
			stage('Promote to Green') {
				agent {label 'centos_slave'}
				steps {
					sh 'cp /var/www/html/rectangles/all/rectangle_${BUILD_NUMBER}.jar /var/www/html/rectangles/green/ '
				}
			}
	}	
}		
