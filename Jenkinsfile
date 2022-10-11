pipeline {
    agent any

    environment {
        VERSION = "latest"
        registry = "registry.heroku.com/test-pipeline/web"
    }

    stages {
        stage('build-test') {
            steps {
                sh "npm install -g pnpm"
                sh "pnpm install"
                sh "pnpm build"
                sh "pnpm test"
            }
        }

        stage('deploy') {
          sh "export VERSION=\$(node -e \"console.log(require('./package.json').version)\")"
          script {
            docker.withRegistry('https://registry.heroku.com', 'herokuId') {
              sh "docker buildx build --platform linux/amd64 -t ${registry}:${VERSION} ."
              sh "docker push ${registry}:${VERSION}"
            }

          withCredentials([usernamePassword(credentialsId: 'herokuId', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
              // sh "echo ${USERNAME}  echo ${PASSWORD} | heroku login"
              sh "HEROKU_API_KEY=${PASSWORD} npx heroku container:release web --app=joke-jenkins"
            }
          }

        }
    }
}

