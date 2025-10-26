pipeline {
  agent any

  tools {
    // Ini sudah benar dan akan mengatur PATH untuk semua perintah 'sh'
    dockerTool 'docker-default'
  }

  environment {
    // Kita tidak perlu 'PATH' di sini lagi, karena 'tools' sudah menanganinya.
    IMAGE_NAME = 'nunu13/praktikum-docker-jenkins'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials' // Pastikan ID kredensial Anda benar
    // Variabel REGISTRY tidak diperlukan lagi
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
          // Ini sudah berhasil dan tidak diubah
          docker.build("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }

    // ===================================================================
    // INI ADALAH PERUBAHAN UTAMA UNTUK MEMPERBAIKI ERROR
    // Kita ganti 'docker.withRegistry' yang bermasalah
    // dengan perintah 'sh' (shell) manual.
    // ===================================================================
    stage('Push Docker Image') {
      steps {
        // Kita gunakan 'withCredentials' untuk mengambil username/password
        // 'dockerhub-credentials' adalah ID kredensial "Username with password" Anda
        withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, 
                                          usernameVariable: 'DOCKER_USER', 
                                          passwordVariable: 'DOCKER_PASS')]) {
          
          // 1. Login menggunakan perintah 'sh'
          // Perintah 'sh' dijamin akan menemukan 'docker' karena blok 'tools'
          sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"

          // 2. Definisikan tag dan push
          def tag = "${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
          sh "docker push ${tag}"

          // 3. Buat tag 'latest' dan push
          sh "docker tag ${tag} ${env.IMAGE_NAME}:latest"
          sh "docker push ${env.IMAGE_NAME}:latest"
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