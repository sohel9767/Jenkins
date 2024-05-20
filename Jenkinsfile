pipeline {
    agent any
    stages {
        stage {'build maven'} {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }
        stage {'Copy Artifact'} {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage {'Build Docker Image'} {
            steps {
                script {
                    def customImage = docker.build("sohelp/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    customImage.push('latest') 
                    }
                }
            }
        }
        stage {'Build on kubernetes'} {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    sh 'cp -R helm/* .'
                    sh 'ls -lrth'
                    sh 'pwd'
                    sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repo=sohelp/petclinic --set image.tag=latest'
                }
            }
        }
    } 
}