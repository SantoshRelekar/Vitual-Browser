pipeline {
    agent any
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git check out') {
            steps {
               git branch: 'main', url: 'https://github.com/SantoshRelekar/Vitual-Browser.git'
            }
        }
         stage('OWASP Dependency check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableNodeAudit', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/Dependency-check-report.xml'
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser \
                           -Dsonar.projectName=VirtualBrowser   '''
                }
            }
        }
        stage('Docker build & Tag Image') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker-login', toolName: 'docker') {
                       dir('/var/lib/jenkins/workspace/virtual-browser/.docker/firefox') {
                           sh "docker build -t devopsmaster1/vb:latest ."
                       }
                   }
               }
               
            }
        }
        stage('Scan Docker image Trivy') {
            steps {
               sh "trivy image devopsmaster1/vb:latest "
            }
        }
        stage('Docker push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker-login', toolName: 'docker') {
                        sh "docker push devopsmaster1/vb:latest"
                   }
               }
               
            }
        }
       
    }
}
