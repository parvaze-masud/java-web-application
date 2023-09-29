pipeline {
    agent any
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/parvaze-masud/java-web-application.git'
            }
        }
        
        stage('Code compile') {
            steps {
                sh'mvn clean compile'
            }
        }
        
        stage('Code build') {
            steps {
                sh'mvn clean install'
            }
        }
        
        stage('Docker image build') {
            steps {
                sh'docker build -t java-webapp-img .'
            }
        }
        
        stage("Push to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHubCred",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag java-webapp-img ${env.dockerHubUser}/java-webapp-img:latest"
                    sh "docker push ${env.dockerHubUser}/java-webapp-img:latest"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh"docker run -d -p 8086:8080 --name jave-webapp-cont java-webapp-img:latest"
            }
        }
    }
}
