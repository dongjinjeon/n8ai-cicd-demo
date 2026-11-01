pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: kaniko
            image: gcr.io/kaniko-project/executor:debug
            command: ["sleep"]
            args: ["99d"]
            volumeMounts:
            - name: docker-config
              mountPath: /kaniko/.docker
          volumes:
          - name: docker-config
            secret:
              secretName: harbor-regcred
              items:
              - key: .dockerconfigjson
                path: config.json
      '''
    }
  }
  stages {
    stage('Build & Push') {
      steps {
        container('kaniko') {
          sh '''
          /kaniko/executor \
            --context=`pwd`/app \
            --dockerfile=`pwd`/app/Dockerfile \
            --destination=harbor.n8ai.org:8445/demo/n8ai-demo:${BUILD_NUMBER} \
            --destination=harbor.n8ai.org:8445/demo/n8ai-demo:latest
          '''
        }
      }
    }
  }
}
