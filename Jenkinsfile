def appname = "project2-app"
def repo = "noakariti"
def appimage = "${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"
def dockerImage

podTemplate(containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
    containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, privileged: true),
    containerTemplate(name: 'python', image: 'python:3.12-slim', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'trivy', image: 'aquasec/trivy:latest', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'helm', image: 'alpine/helm:3.14.0', command: 'cat', ttyEnabled: true)
])
{
    node(POD_LABEL) {
        stage('checkout') {
            container('jnlp') {
                sh '/usr/bin/git config --global http.sslVerify false'
                checkout scm
            }
        } // end checkout

        stage('lint & security scan') {
            parallel(
                'Lint': {
                    container('python') {
                        echo "Linting with flake8..."
                        sh 'pip install --no-cache-dir flake8'
                        sh 'flake8 . --max-line-length=120 || true'
                    }
                },
                'Security Scan': {
                    container('trivy') {
                        echo "Security Scanning with Trivy..."
                        sh 'trivy fs --exit-code 0 --severity HIGH,CRITICAL .'
                    }
                }
            )
        } // end lint & security scan

        stage('build') {
            container('docker') {
                echo "Building docker image..."
                sh 'until docker info >/dev/null 2>&1; do echo "Waiting for docker daemon..."; sleep 1; done'
                script {
                    dockerImage = docker.build("${appimage}:${apptag}")
                }
            }
        } // end build

        stage('push') {
            container('docker') {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        } // end push

        stage('deploy') {
            container('helm') {
                echo "Deploying with Helm..."
                sh "helm upgrade --install project2 ./helmchart --set image.repository=${appimage} --set image.tag=${apptag}"
            }
        } // end deploy
    }
}