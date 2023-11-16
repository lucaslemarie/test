pipeline {
  agent any
  stages {
    stage('Cloner repository') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/lucaslemarie/test.git']]])
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          docker.build('image-jenkins-tp')
        }

      }
    }

    stage('Analyze image via Trivy') {
      steps {
        sh 'docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image image-jenkins-tp'
      }
    }

    stage('Deploy nodejs') {
      when {
        expression {
          currentBuild.resultIsBetterOrEqualTo('SUCCESS')
        }

      }
      steps {
        script {
          def container = docker.image('test-image-jenkins').run("--name test-auto-jenkins -p 8000:80 -d")
        }

      }
    }

    stage('Deploy on Discord') {
      steps {
        script {
          def webhookUrl = 'https://discord.com/api/webhooks/1174700530375856229/KBM8TOaUn7dL5v_-RtBZpqJ0igPTPPTHbGDTwD0FE_Py_n6od2TfG2NIJV9ydrgkZL9q' // Remplacez par votre URL de webhook Discord

          def message = '{"content":"Build OK"}' // Le message que vous souhaitez envoyer

          def response = sh(script: "curl -X POST -H 'Content-Type: application/json' -d '${message}' ${webhookUrl}", returnStdout: true)
          echo "Response from Discord: ${response}"
        }

      }
    }

  }
}