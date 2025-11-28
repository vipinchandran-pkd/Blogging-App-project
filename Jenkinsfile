pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven'
    }
    environment{
      SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('git checkout') {
            steps {
               git branch: 'main', credentialsId: 'git', url: 'https://github.com/didin2003/Blogging-App.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Trivy file scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }
        stage('Sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                sh """
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=BloggingApp \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.host.url=http://16.16.251.145:9000 \
                    -Dsonar.login=sqp_55e2328477212ad39a86b24286dc0a029032cc55
                """
               }
            }
        }
        stage('build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publish artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                 }
            }
        }
        stage('Build and Tag to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker build -t didin8080/blogging-app:latest ."
                    }
                }
            }
        }
        stage('Trivy image scan') {
            steps {
                sh "trivy image --format table -o image.html didin8080/blogging-app:latest"
            }
        }
        stage('Push to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker push didin8080/blogging-app:latest"
                    }
                }
            }
        }
        stage('k8-build') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.22.204:6443'){ 
                    sh "kubectl apply -f deployment-service.yml"
                    sleep 20
                }
            }
        }
        stage('k8-verify') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.22.204:6443'){
                    sh "kubectl get pods"
                    sh "kubectl get svc"
                }
            }
        }
    }
     post {
    always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: """
                <html>
                <body>
                    <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                    </div>
                    <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                    </div>
                    <div style="background-color: #87CEEB; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">URL: ${env.BUILD_URL}</p>
                    </div>
                </body>
                </html>
            """,
            to: 'didinpg8080@gmail.com',
            mimeType: 'text/html',
            attachmentsPattern: 'trivy.txt'
        }
    }
}
