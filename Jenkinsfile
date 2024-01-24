pipeline {
    agent any
      stages {
        stage('Checkout') {
            steps {
                script {
                    git credentialsId: 'git-credenails', url: 'https://github.com/Merit516/new-app.git'
                }
            }
        }

    stages {
        stage('build') {
            steps {
                echo 'build'
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME_DOCKER', passwordVariable: 'PASSWORD_DOCKER')]) {
                    sh '''
                        docker login -u ${USERNAME_DOCKER} -p ${PASSWORD_DOCKER}
                        docker build -t merit237/new-app:v${BUILD_NUMBER} .
                        docker push merit237/new-app:v${BUILD_NUMBER}
                    '''
                }
            }
        }
         stage('deploy') {
            
            steps {
                echo 'deploy'
                withCredentials([file(credentialsId: 'openshift-config', variable: 'OPENSHIFT_CONFIG')]) {
                    sh '''
                        mv Deployment/deploy.yaml Deployment/tmp.yaml
                        cat Deployment/tmp.yaml | envsubst > Deployment/deploy.yaml
                        rm -f Deployment/tmp.yaml
                        oc apply -f Deployment --kubeconfig ${OPENSHIFT_CONFIG} -n merit
                    '''
                }
            }
        }
        
    }
}
