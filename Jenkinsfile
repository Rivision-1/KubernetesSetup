pipeline {
  agent any
  stages { 
    stage('Cleanup') {
      steps {
        script {
          timeout(time: 5, unit: 'SECONDS'){
          input("You are cleaning up ${EnvironmentNamespace} environment  Are you sure you want to proceed?")
          }
          withKubeConfig([credentialsId: KubeconfigId, namespace: EnvironmentNamespace]) {
          sh '''
          #!/bin/bash
          deploys=$(kubectl get deploy -o=name | egrep \'*-api\' | grep -v \'apispecification\\|rules\\|tableau\')
          for deploy in $deploys; do kubectl scale $deploy --replicas=0; done
          stss=$(kubectl get sts -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          for sts in $stss; do kubectl scale $sts --replicas=0; done
          pvcs=$(kubectl get pvc -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          sleep 30
          for pvc in $pvcs; do kubectl delete $pvc; done
          sleep 10
          for sts in $stss; do kubectl scale $sts --replicas=1; done
          sleep 5
          dbs=$(kubectl get pods -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          for db in $dbs; do kubectl wait --for=condition=ready --timeout=300s $db; done
          for deploy in $deploys; do kubectl scale $deploy --replicas=1; done'''
          }
        }
      }
    }
  }
}
