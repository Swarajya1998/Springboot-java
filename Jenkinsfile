pipeline {
    agent any

    environment {
        IMAGE = "swarajyadoc/fuzentrix-springboot-java"
        TAG = "v${BUILD_NUMBER}"
        KUBECONFIG = '/var/jenkins_home/.kube/config'
    }

    stages {

        stage('Clone Java Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Swarajya1998/springboot-java.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE:$TAG
                    '''
                }
            }
        }

        stage('Clone K8 Repo') {
            steps {
                dir('k8') {
                    git branch: 'main', url: 'https://github.com/Swarajya1998/k8-my-app.git'
                }
            }
        }

        stage('Deploy using Helm') {
            steps {
                dir('k8') {
                    sh '''
                    pwd
                    ls
                    helm upgrade --install my-app ./my-app-charts \
                    --set image.repository=$IMAGE \
                    --set image.tag=$TAG
                    '''
                }
            }
        }
    }
}