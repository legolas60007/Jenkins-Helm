pipeline {
    agent any
  
    stages {
        stage ("checkout from GIT") {
            steps {
                sh 'echo Checkout Correcto' 
            }
        }
      
        stage ("Kubernetes Export") {
            steps {
                sh 'aws eks --region eu-west-1 update-kubeconfig --name demo'
            }
        }
        stage ("Helm Deploy") {
            steps {
                sh  'helm repo add bitnami https://charts.bitnami.com/bitnami' 
                sh   'helm install my-release bitnami/prestashop'
                sh   'export APP_HOST=$(kubectl get svc --namespace default my-release-prestashop --template "{{ range (index .status.loadBalancer.ing{{ end }}")'
                sh   'export APP_PASSWORD=$(kubectl get secret --namespace default my-release-prestashop -o jsonpath="{.data.prestashop-password}" | base64 -d)'
                sh   'export DATABASE_ROOT_PASSWORD=$(kubectl get secret --namespace default my-release-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d)'
                sh   'export APP_DATABASE_PASSWORD=$(kubectl get secret --namespace default my-release-mariadb -o jsonpath="{.data.mariadb-password}" | base64 -d)'
                sh  'helm upgrade --namespace default my-release bitnami/prestashop \
    --set prestashopHost=$APP_HOST,prestashopPassword=$APP_PASSWORD,mariadb.auth.rootPassword=$DATABASE_ROOT_PASSWORD,mariadb.auth.password=$APP_DATABASE_PASSWORD'
            }
        }
    }
}
