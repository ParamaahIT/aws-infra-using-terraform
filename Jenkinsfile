pipeline {
  agent any

  environment {
    IMAGE_NAME = 'prashanth2paramaah/hello-world-app:latest'
    VAULT_CRED_ID = 'vault-approle-creds'
    VAULT_ADDR    = 'http://44.212.14.235:8200'
  }

  stages {
    stage('Docker Login') {
      steps {
        script {
          withVault(
            vaultUrl: "${env.VAULT_ADDR}",
            vaultCredentialId: "${env.VAULT_CRED_ID}",
            vaultSecrets: [
              [
                path: 'docker-credentials',     // ✅ Do NOT prefix with 'secret/'
                engineVersion: 2,               // KV v2
                secretValues: [
                  [envVar: 'DOCKER_USERNAME', vaultKey: 'username'],
                  [envVar: 'DOCKER_PASSWORD', vaultKey: 'password']
                ]
              ]
            ]
          ) {
            sh '''
              echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
            '''
          }
        }
      }
    }
    stage('Build Docker Image') {
      steps {
         sh 'docker build -t $IMAGE_NAME .'
      }
    }
  }
}
