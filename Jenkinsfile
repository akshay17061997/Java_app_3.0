@Library('my-shared-library') _

pipeline {
    agent any

    parameters {
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "Name of the Docker image", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "Tag of the Docker image", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "DockerHub username", defaultValue: 'praveensingam1994')
    }

    stages {
        stage('Git Checkout') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    gitCheckout(
                        branch: "main",
                        url: "https://github.com/praveen1994dec/Java_app_3.0.git"
                    )
                }
            }
        }
        
        stage('Unit Test: Maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnTest()
                }
            }
        }
        
        stage('Integration Test: Maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnIntegrationTest()
                }
            }
        }
        
        stage('Static Code Analysis: SonarQube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    statiCodeAnalysis('sonarqube-api')
                }
            }
        }
        
        stage('Quality Gate Status Check: SonarQube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    QualityGateStatus('sonarqube-api')
                }
            }
        }
        
        stage('Build: Maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnBuild()
                }
            }
        }
        
        stage('Docker Image Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerBuild(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }
        
        stage('Docker Image Scan: Trivy') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageScan(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }
        
        stage('Docker Image Push: DockerHub') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'akshay506', passwordVariable: '@Summer2023')]) {
                        dockerImagePush(params.ImageName, params.ImageTag, params.DockerHubUser)
                    }
                }
            }
        }
        
        stage('Docker Image Cleanup') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageCleanup(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }
    }
}
