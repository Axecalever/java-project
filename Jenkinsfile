pipeline {
	agent none
	environment {
		MAJOR_VERSION = 1
	}
	
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
					sh "if ![ -d '/var/www/html/rectangles/all/${BRANCH_NAME}']; then mkdir /var/www/html/rectangles/all/${BRANCH_NAME}; fi" 
					sh 'cp dist/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/all/${BRANCH_NAME}'
				}		
			}		
			
			stage('Running on Centos') {
				agent {label 'centos_slave'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/${BRANCH_NAME}/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar'
					sh 'java -jar rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar 3 4'
				}		
			}
			
			stage('Test on Debian') {
				agent {docker 'openjdk:8u121-jre'}
				steps {				
					sh 'wget http://icemanxflow4.mylabserver.com/rectangles/all/${BRANCH_NAME}/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar'
					sh 'java -jar rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar 3 4'
				}		
			}
			
			stage('Promote to Green') {
				agent {label 'centos_slave'}
				when {
					branch 'master'
				}
				steps {
					sh 'cp /var/www/html/rectangles/all/${BRANCH_NAME}/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/green/ '
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
					sh 'git checkout master'
					sh 'git pull origin '
					echo 'Merging development into Master Branch'
					sh 'git merge development'
					sh 'git push origin master '
					sh "git tag rectangle-${MAJOR_VERSION}.${BUILD_NUMBER}"
					sh 'git push origin rectangle-${MAJOR_VERSION}.${BUILD_NUMBER}'
				}
				post {
        success {
          emailext(
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted to Master",
            body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Development Promoted to Master":</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
            to: "ahsansyed1992@gmail.com"
          )
        }
      }
    }
  }
  post {
    failure {
      emailext(
        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
        body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        to: "ahsansyed1992@gmail.com"
      )
    }
  }
}