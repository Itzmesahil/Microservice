// pipeline {
//     agent any

//     stages {
//         stage('Deploy to K8s') {
//             steps {
//                 withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9220C4AC73910DFA88F4E76A45EB431F.gr7.ap-south-1.eks.amazonaws.com']]) {
//                     sh "kubectl apply -f ."
//                     sleep 60 

//                 }
//             }
//         }
//         stage("Verify Deployment") {
//             steps {
//                 withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9220C4AC73910DFA88F4E76A45EB431F.gr7.ap-south-1.eks.amazonaws.com']]) {
//                     sh "kubectl get svc -n webapps"
//                 }
//             }
//         }
//     }
// }

pipeline {
    agent any

    stages {
        stage('Wait for Other Branches') {
            steps {
                script {
                    // Check if this is the main branch
                    if (env.BRANCH_NAME == 'main') {
                        // Wait for other branches to complete
                        waitUntil {
                            // Replace 'branch-name-pattern' with your specific branch naming pattern
                            def jobs = Jenkins.instance.getAllItems(Job.class)
                            def otherBranchesRunning = jobs.any { job -> 
                                job.name.startsWith('your-branch-prefix') && 
                                job.isBuilding() 
                            }
                            return !otherBranchesRunning
                        }
                    }
                }
            }
        }
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    def services = [
                        'emailservice', 
                        'checkoutservice', 
                        'recommendationservice', 
                        'frontend', 
                        'paymentservice', 
                        'productcatalogservice', 
                        'cartservice', 
                        'loadgenerator', 
                        'currencyservice', 
                        'shippingservice', 
                        'redis-cart', 
                        'adservice'
                    ]
                    
                    for (service in services) {
                        sh """
                        docker build -t itzmesahil/${service}:${env.BUILD_ID} ./${service}
                        docker push itzmesahil/${service}:${env.BUILD_ID}
                        """
                    }
                }
            }
        }
        stage('Update Manifests with New Tags') {
            steps {
                script {
                    def newTag = env.BUILD_ID 
                    def services = [
                        'emailservice', 
                        'checkoutservice', 
                        'recommendationservice', 
                        'frontend', 
                        'paymentservice', 
                        'productcatalogservice', 
                        'cartservice', 
                        'loadgenerator', 
                        'currencyservice', 
                        'shippingservice', 
                        'redis-cart', 
                        'adservice'
                    ]

                    for (service in services) {
                        sh """
                        sed -i 's|image: itzmesahil/${service}:.*|image: itzmesahil/${service}:${newTag}|' path/to/manifests/${service}/manifest.yaml
                        """
                    }
                }
            }
        }
        stage('Commit and Push Changes') {
            steps {
                script {
                    sh """
                    git config user.name 'Jenkins'
                    git config user.email 'jenkins@example.com'
                    git add path/to/manifests/*
                    git commit -m 'Updated image tags for build ${env.BUILD_ID}'
                    git push origin main // Change to your branch
                    """
                }
            }
        }
    }
}

