@Library("shared")_
pipeline {
    agent {label "dev"};
    environment {
        ARTIFACTORY_SERVER = 'Artifactory' // Your configured server ID
    }
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
        stage('Upload to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server(ARTIFACTORY_SERVER)

                    def uploadSpec = """{
                      "files": [
                        {
                          "pattern": "/home/ubuntu/jenkins/workspace/demo-cicd/*",
                          "target": "generic/my-app/"
                        }
                      ]
                    }"""

                    def buildInfo = server.upload(spec: uploadSpec)
                    server.publishBuildInfo(buildInfo)
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
