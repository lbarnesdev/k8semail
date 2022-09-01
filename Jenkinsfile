properties([pipelineTriggers([githubPush()])])

pipeline {
    agent {
        kubernetes {
            yaml """
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - 99d
    volumeMounts:
    - name: jenkins-docker-cfg
      mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    secret:
        secretName: docker-credentials
        items:
        - key: .dockerconfigjson
          path: config.json
  
"""
        }
    }
    triggers {
        GenericTrigger(
            genericVariables: [
                [key: 'ref', value: '$.ref']
            ],

            causeString: 'Triggered on $ref',

            token: 'k8semail',
            tokenCredentialId: '',

            printContributedVariables: true,
            printPostContent: true,

            silentResponse: false,

            regexpFilterText: '$ref',
            regexpFilt4erExpression: 'refs/heads/' + BRANCH_NAME
        )
    }
    stages {
        stage('Build with Kaniko') {
            when { changeset "docker/postfix/Dockerfile" }
            steps {
                git branch: 'main',
                    credentialsId: 'github-app-lbarnesdev',
                    url: 'https://github.com/lbarnesdev/k8semail.git'
                container(name: 'kaniko', shell: '/busybox/sh') {
                    sh '''#!/busybox/sh
                    /kaniko/executor --force --context `pwd` --destination lbarnesdev/postfix:latest --dockerfile docker/postfix/Dockerfile
                    '''
                }
            }
        }
    }
}