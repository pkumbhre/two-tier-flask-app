pipeline {
    agent {label "dev"};
    stages {
        stage("Code clone"){
            steps {
                echo "Downloding code"
                git url:"https://github.com/pkumbhre/two-tier-flask-app.git", branch:"master"
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
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser"
                    )]){
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker tag two-tier-flask-app ${env.dockerHubUser}/two-tier-flask-app"
                sh "docker push ${env.dockerHubUser}/two-tier-flask-app:latest"
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker compose up -d --build flask-app "
            }
        }
    }
}
