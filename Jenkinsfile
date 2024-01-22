def remote = [name: 'K8S_Master', host: '18.118.26.92', user: 'kube', password: 'admin123', allowAnyHosts: true]
pipeline{
    agent any
    tools {
        maven "maven3.9.1"
    }
    stages{
        stage('1. Git Clone'){
            steps{
                git credentialsId: 'GitHubCred', url: 'https://github.com/MerciBoi-Lives/spring-boot-docker-mine'
            }
        }
        stage('2. Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('3. Run Code Quality Analysis'){
            steps{
                sh "mvn sonar:sonar"
            }
        }
        stage('4. Upload Artifacts to Nexus'){
            steps{
                sh "mvn install"
            }
        }
        stage('5. Build Docker Image'){
            steps{
                sh "docker build . -t chydinma/spring-boot-mongo:10"
            }
        }
        stage('6. DockerHub Login'){
            steps{
                withCredentials([string(credentialsId: 'DockerHub password', variable: 'DOCKERHUB_PASSWORD')]) {
                sh "docker login -u chydinma -p ${DOCKERHUB_PASSWORD}"
                }
            }
        }
        stage('7. Push to DokerHub Registry'){
            steps{
                sh "docker push chydinma/spring-boot-mongo:10"
            }
        }
        stage('8. SSH onto K8S_Master & Deployment'){
            steps{
                sshPut remote: remote, from: '/var/lib/jenkins/workspace/Demo-Spring/springboot.yml', into: '.'
                sshCommand remote: remote, command: "kubectl apply -f springboot.yml"
                }
            }
        }
} 
