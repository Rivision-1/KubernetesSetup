pipeline {
	options {
		buildDiscarder(logRotator(numToKeepStr: '$builds'))
			}
        stages {
         stage('Test') {
            steps {
	     script {
                sh """ls"""
	            }
          }
        }
      }
   }
