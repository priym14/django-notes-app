pipeline {
    agent any
    environment { SONAR_SCANNER = tool "sonarqube-scanner"
}
    stages{
        stage("Clonnin the repo"){
            steps{
                 git url:"https://github.com/priym14/django-notes-app.git", branch:"main"
            }
        }
        stage("code check with sonarqube"){
            steps{
                withSonarQubeEnv("sonarqube-scanner") {
                  sh '''$SONAR_SCANNER/bin/sonar-scanner \
                  -Dsonar.projectName=django-notes-app \
                  -Dsoanr.projectKey=django-notes-app'''
            }
        }
        }
        stage("Building the Docker image"){
            steps{
                echo "Building the Docker image"
                sh 'docker build -t django-app .'
            }
        }
        stage("Logging and pushing the image to Docker hub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                echo "tagging the docker image"
                sh 'docker tag django-app $dockerHubUser/django-app:$BUILD_NUMBER'
                echo "logging into the Docker image"
                sh 'docker login -u $dockerHubUser -p $dockerHubPass'
                echo "pushing the Docker image"
                sh 'docker push $dockerHubUser/django-app:$BUILD_NUMBER'
                }
            }
        }
        stage("Deploy"){
            steps {
                echo "Deploying the container"
                sh "docker-compose down && docker-compose up -d"
                
            }
        }
    }
}
