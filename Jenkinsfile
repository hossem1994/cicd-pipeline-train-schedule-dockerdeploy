pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                node {
                    checkout scm
                    def customImage = docker.build("99409cdf561c/train-scheduler")
                    customImage.inside {
                    sh 'echo $(curl localhost:8080)'
                  }
               }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                node {
                    checkout scm
                    docker.withRegistry('https://registry.hub.docker.com/','docker_hub_login')
                    customImage.push("${env.BUILD_NUMBER}")
                    customImage.push("latest")
                  }
               }
            }
        }
    }
}
