pipeline {
    agent any
    environment {
        PROJECT_ID = 'sswu-open-source-project2024'
        CLUSTER_NAME = 'k8s'
        LOCATION = 'asia-northeast3-a'
        CREDENTIALS_ID = 'gke'
        GITHUB_TOKEN = credentials('GitHub')  // 설정한 GitHub Token
    }
    stages {
        stage("Chechout code") {
		    steps {
                chechout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("jjwon0407/nogeut:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'jjwon0407') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to GKE') {
            when {
                branch 'main'
            }
            steps {
                sh "sed -i 's/nogeut:latest/nogeut:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }       
    }    
}