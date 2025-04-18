pipeline {
    agent any
    
    environment { 
        DOCKERHUB_UN = 'mostafamedhat1'
    }
    
    stages {
        stage('Build & Test') {
            steps {
                sh '''
                docker image build -t $DOCKERHUB_UN/image:${GIT_COMMIT} ./app
                '''
            }
        }
        
        stage('Delivery') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Docker_Creds', usernameVariable: 'DOCKERHUB_UN', passwordVariable: 'DOCKERHUB_PASS')]) {  
                    sh '''
                    docker login -u $DOCKERHUB_UN -p $DOCKERHUB_PASS
                    docker push $DOCKERHUB_UN/image:${GIT_COMMIT}
                    '''
                }
            }
        }
        
        
    
        stage('Deploy app with helm') {
            steps {
                withCredentials([
                    string(credentialsId: 'DB_HOST', variable: 'DB_HOST'),
                    string(credentialsId: 'DB_USER', variable: 'DB_USER'),
                    string(credentialsId: 'DB_PASS', variable: 'DB_PASS'),
                    string(credentialsId: 'DB_DATABASE', variable: 'DB_DATABASE'),
                    string(credentialsId: 'db_endpoint', variable: 'db_endpoint'),
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-cred',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]
                ]) {
                    sh """
                        aws eks update-kubeconfig --name python-app-cluster --region us-west-2
                        helm upgrade --install myapp ./k8s \\
                          --set db_endpoint=${db_endpoint} \\
                          --set image=${DOCKERHUB_UN}/image:${GIT_COMMIT} \\
                          --set db_host=${DB_HOST} \\
                          --set db_user=${DB_USER} \\
                          --set db_pass=${DB_PASS} \\
                          --set db_database=${DB_DATABASE}
                        """
                }
            }
        }
        
        stage('Deploy Metrics Server') {
            steps {
                withCredentials([
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-cred',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]
                ]) {
                    sh '''
                    kubectl apply -f ./k8s/metrics-server.yaml
                    kubectl apply -f ./k8s/app-hpa.yaml
                    '''
                }
            }
        }

 stage('Deploy Prometheus and Grafana') {
            steps {
                withCredentials([
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-cred',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]
                ]) {
                    sh '''
                    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
                    helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
                      --namespace monitoring \
                      --create-namespace \
                      --set alertmanager.persistentVolume.storageClass="gp2",server.persistentVolume.storageClass="gp2"
                    kubectl apply -f  ./k8s/grafana-lb.yaml 
                    '''
                }
            }
        }


        
    }
}
