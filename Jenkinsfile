pipeline {
    agent { label 'jenkins-slave' }
    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod',"release"])
    } 
    stages {
        stage('Build') {
            steps {
                script {
                    if (params.ENV == "dev" || params.ENV == "test" || params.ENV == "prod") {
                       withCredentials([usernamePassword(credentialsId: 'hu-dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t husseinghoraba/bakehouse:${BUILD_NUMBER} .
                                docker push husseinghoraba/bakehouse:${BUILD_NUMBER}
                           """
                       }
                    }
                }
               }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (params.ENV == "release") {
                withCredentials([file(credentialsId: 'k8s-conf', variable: 'KUBECONFIG')]) {
                  sh """
                  mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                  cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                  rm -f Deployment/deploy.yaml.tmp
                  kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                  """
                }
            }
        }
    }
}
