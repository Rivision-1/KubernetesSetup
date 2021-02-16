pipeline {
   agent any
  stages { 
    stage('Cleanup') {
      steps {
        script {
           withKubeConfig([credentialsId: KubeconfigId, namespace: EnvironmentNamespace]) {
          sh '''deploys=$(kubectl get deploy -o=name | egrep \'*-api\' | grep -v \'apispecification\\|rules\\|tableau\')
          for deploy in $deploys; do kubectl scale $deploy --replicas=0; done
          stss=$(kubectl get sts -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          for sts in $stss; do kubectl scale $sts --replicas=0; done
          pvcs=$(kubectl get pvc -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          sleep 30
          for pvc in $pvcs; do kubectl delete $pvc; done
          sleep 15
          echo $pvcs | cut -d "/" -f2
          for pvc in $pvcs; do kubectl apply -f -\\nkind: PersistentVolumeClaim\\napiVersion: v1\\nmetadata:\\n\\ \\ name: $pvc\\nspec:\\n\\ \\ accessModes:\\n \\ \\ - ReadWriteOnce\\nresources:\\n \\ \\ requests:\\n \\ \\ storage: 10Gi\\ndone
          sleep 10
          for sts in $stss; do kubectl scale $sts --replicas=1 done
          sleep 200
          for deploy in $deploys; do kubectl scale $deploy --replicas=1 done'''
        }
      }
    }
  }
}
}
