pipeline {
  agent any

  environment {
    IMAGE_NAME = 'prashanth2paramaah/hello-world-app:latest'
    VAULT_CRED_ID = 'vault-approle-creds'
    VAULT_ADDR    = 'http://52.72.235.53:8200'
  }

  stages {
    stage('Docker Login') {
      steps {
        vault(
          vaultSecrets: [
            [
              path: 'docker-credentials',
              engineVersion: 2,
              secretValues: [
                [envVar: 'DOCKER_USERNAME', vaultKey: 'username'],
                [envVar: 'DOCKER_PASSWORD', vaultKey: 'password']
              ]
            ]
          ],
          vaultUrl: "${env.VAULT_ADDR}",
          vaultCredentialId: "${env.VAULT_CRED_ID}"
        )
        sh '''
          echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
        '''
      }
    }

    stage('Build Docker Image') {
      steps {
        sh """
          docker build -t ${IMAGE_NAME} .
        """
      }
    }

    stage('Push Docker Image') {
      steps {
        sh """
          docker push ${IMAGE_NAME}
          docker logout
        """
      }
    }
  }
}

