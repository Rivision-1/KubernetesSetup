pipeline {
   agent any
   //parameters {
     //   choice(name: 'EnvironmentNamespace', choices: 'dev\nqa\nint\nuat')
    //}
  stages { 
    stage('Cleanup') {
      steps {
        script {
          withKubeConfig([credentialsId: KubeconfigId, namespace: ${env.EnvironmentNamespace}])
          sh '''deploys=$(kubectl get deploy | egrep \'*-api\' | grep -v \'apispecification\\|rules\\|tableau\')
          for deploy in $deploys; do kubectl scale deployment $deploy --replicas=0; done
          stss=$(kubectl get sts -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          for sts in $stss; do kubectl scale $sts --replicas=0; done
          pvcs=$(kubectl get pvc -o=name | egrep \'*-db|dwh\' | grep -v airflow)
          sleep 30
          for pvc in $pvcs; do kubectl delete pvc $pvc; done
          sleep 15
          for pvc in $pvcs; do
          cat <<EOF | kubectl apply -f -
          kind: PersistentVolumeClaim
          apiVersion: v1
          metadata:
            name: $pvc
          spec:
            accessModes:
            - ReadWriteOnce 
          resources:
            requests:
              storage: 10Gi
          EOF
          done
          sleep 10
          for sts in $stss; do kubectl scale $sts --replicas=1; done
          sleep 200
          for deploy in $deploys; do kubectl scale deployment $deploy --replicas=1; done'''
        }
      }
    }
  }
}
