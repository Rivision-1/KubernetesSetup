pipeline {
   agent any
  stages { 
    stage('Cleanup') {
      steps {
        script {
           withKubeConfig([credentialsId: KubeconfigId, namespace: EnvironmentNamespace]) {
          
          sh '''kubectl get po -n dev'''
        }
      }
    }
  }
}
}
