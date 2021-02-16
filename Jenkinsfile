pipeline {
   agent any
  stages { 
    stage('Cleanup') {
      steps {
        script {
           withKubeConfig([credentialsId: KubeconfigId, namespace: EnvironmentNamespace]) {
          sh '''deploys=$(kubectl get deploy | egrep \'*-api\' | grep -v \'apispecification\\|rules\\|tableau\')'''
          
        }
      }
    }
  }
}
}
