pipeline {
  agent any

  tools {
    // Ini sudah benar
    dockerTool 'docker-default'
  }

  environment {
    PATH = "/usr/local/bin:${env.PATH}"
    IMAGE_NAME = 'nunu13/praktikum-docker-jenkins'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials'
  }
  
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'echo "Mulai build aplikasi"'
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          docker.build("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }
    stage('Push Docker Image') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, 
                                            usernameVariable: 'DOCKER_USER', 
                                            passwordVariable: 'DOCKER_PASS')]) {
            
            // ===================================================================
            // INI ADALAH PERBAIKANNYA:
            // 
            // 1. Kita gunakan 'sh '...' (kutip tunggal) untuk menghindari warning.
            //    Ini membuat $DOCKER_USER dan $DOCKER_PASS dibaca sebagai
            //    variabel environment SHELL, bukan interpolasi Groovy.
            // 2. Kita gunakan 'echo $DOCKER_PASS | ... --password-stdin'
            //    Ini adalah cara login yang aman dan memperbaiki error sintaks Anda.
            // ===================================================================
            sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
  
            // Baris-baris ini sudah benar dan tidak perlu diubah
            def tag = "${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
            sh "docker push ${tag}"
  
            sh "docker tag ${tag} ${env.IMAGE_NAME}:latest"
            sh "docker push ${env.IMAGE_NAME}:latest"
          }
        }
      }
    }
  }
  post {
    always {
      echo 'Selesai build'
    }
  }
}