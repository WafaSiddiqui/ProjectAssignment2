pipeline {
    agent any
    tools{
    jdk "myjdk8"
    }
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "wafasidd/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
           
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy to K8s')
      {
        steps{
          sshagent(['k8s-jenkins'])
          {
            sh 'scp -r -o StrictHostKeyChecking=no node-deployment.yaml username@102.10.16.23:/path'script{
              try{
                sh 'ssh username@102.10.16.23 kubectl apply -f /path/node-deployment.yaml --kubeconfig=/path/kube.yaml'}
              catch(error)
              {}
            }
          }
        }
      }
    }
