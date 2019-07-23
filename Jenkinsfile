pipeline {
    agent any
    stages {
        stage('Building Docker Image') {
            steps {
                container('docker') {
                    sh "docker build -t mycluster.icp:8500/default/riskcalc:v${env.BUILD_NUMBER} ."
                }
            }
        }
        stage('Pushing image to ICP Registry') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'registry-secret',
                                    usernameVariable: 'USERNAME',
                                    passwordVariable: 'PASSWORD')]) {
                        sh "docker login -u ${USERNAME} -p ${PASSWORD} mycluster.icp:8500"
                        sh "docker push mycluster.icp:8500/default/riskcalc:v${env.BUILD_NUMBER}"
                    }
                }
            }
        }
        stage("Delivery Application on ICP") {
            steps {
                container('kubectl') {
                    sh "kubectl create deployment riskcalc-deployment --image=mycluster.icp:8500/default/risk_calc:v${env.BUILD_NUMBER} -n default"
                    sh "kubectl expose deployment riskcalc-deployment --name=riskcalc-service --type=LoadBalancer --port=8888 -n default"
                }
            }
        }
    }
}
