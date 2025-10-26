pipeline {
  agent any

  tools {
    // Ini sudah benar
    dockerTool 'docker-default'
  }

  environment {
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
          // Ini sudah benar
          docker.build("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }

    // ===================================================================
    // PERBAIKAN DI SINI:
    // Seluruh logika (termasuk 'withCredentials' dan 'def')
    // harus dibungkus dalam blok 'script'
    // ===================================================================
    stage('Push Docker Image') {
      steps {
        // Tambahkan 'script' block ini
        script {
          
          withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, 
                                            usernameVariable: 'DOCKER_USER', 
                                            passwordVariable: 'DOCKER_PASS')]) {
            
            // 1. Login
            sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
  
            // 2. Definisikan tag dan push
            // Sekarang 'def' ini valid karena ada di dalam 'script'
            def tag = "${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
            sh "docker push ${tag}"
  
            // 3. Buat tag 'latest' dan push
            sh "docker tag ${tag} ${env.IMAGE_NAME}:latest"
            sh "docker push ${env.IMAGE_NAME}:latest"
          }
        }
      }
    }
    // ===================================================================

  }
  post {
    always {
      echo 'Selesai build'
    }
  }
}