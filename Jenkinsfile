pipeline {
    environment {
        registry = "s17947/jenkins-docker-spring-test"
        DOCKERHUB_CREDENTIALS = credentials('docker-login-pwd')
    }
    agent {
        docker {
            image 'maven:3-openjdk-17'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage("Build"){
            steps {
                sh 'mvn -f pom.xml clean package -DskipTests'
            }
        }
        stage("Test"){
            steps {
                sh 'mvn test'
            }
        }
        stage("Build & Push Docker image") {
            steps {
                sh 'docker image build -t $registry:$BUILD_NUMBER .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u s17947 --password-stdin'
                sh 'docker image push $registry:$BUILD_NUMBER'
                sh "docker image rm $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploy and smoke test') {
            steps{
                sh 'chmod +x ./jenkins/scripts/*.sh'
                sh './jenkins/scripts/deploy.sh'
            }
        }
        stage('Cleanup') {
            steps{
                sh './jenkins/scripts/cleanup.sh'
            }
        }
    }
}