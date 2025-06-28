@Library("shared")_
pipeline {
    agent {label "dev"};
    stages {
        stage("Code clone"){
            steps {
                echo "Downloding code"
                script {
                    clone("https://github.com/pkumbhre/two-tier-flask-app.git", "master")
                }
            }
        }
        stage("Trivy filesystem scan") {
            steps {
                echo "scanning filesystem"
                script {
                    trivy_fs() 
                }
            }
        }
        stage("Code Build") {
            steps {
                sh "docker build -t two-tier-flask-app ."
            }
        }
        stage("Test"){
            steps {
                echo "Testing done"
            }
        }
        stage("Push to docker hub") {
            steps {
                echo "Image pushing to docker hub" 
                script {
                    docker_push("dockerHubCreds", "two-tier-flask-app")
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker compose up -d --build flask-app "
            }
        }
    }
    post {
        success {
            script {
                emailext from: 'pradeep.kumbhre@gmail.com',
                to: 'p@yopmail.com',
                body: "build sucessful",
                subject: "Build successful"
            }
        }
        failure {
            script {
                emailext from: 'pradeep.kumbhre@gmail.com',
                to: 'smileyshilpa.dhurve@gmail.com',
                body: 'Build failed',
                subject: 'build failed'
            }
        }
    }
}
