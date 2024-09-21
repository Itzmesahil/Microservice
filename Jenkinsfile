pipeline {
    agent any

    stages {
        stage('Deploy to K8s') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9220C4AC73910DFA88F4E76A45EB431F.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl apply -f ."
                    sleep 60 

                }
            }
        }
        stage("Verify Deployment") {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9220C4AC73910DFA88F4E76A45EB431F.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl get all -n webapps"
                }
            }
        }
    }
}
