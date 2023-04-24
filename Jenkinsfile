pipeline{
  agent any
  tools {
    maven 'maven3.8.6'
  }
  environment {
        DOCKER_IMAGE = 'peteruwabor/cohort10:1'
    }
  options {
    timeout(time: 20, unit:"MINUTES")
  }
  stages {
    stage('Cloning'){
      steps {
        echo 'Cloning from GitHub'
        git credentialsId: 'git_cred', url: 'https://github.com/peteruwabor/web-app.git'
        echo 'Cloning done'
      }
    }
    stage('Building'){
      steps {
        echo "packaging our artifact"
        sh 'mvn clean package'
        echo "Packing done"
      }
    }
    stage('Testing'){
      steps {
        echo 'Testing with Sonarqube'
        sh 'mvn sonar:sonar'
        echo 'Testing done'
      }
    }
    stage('Deploying'){
      steps {
        echo 'Deploying to nexus'
        sh 'mvn deploy'
        echo 'Deploying done'
      }
    }
    stage('Approval'){
      steps {
        echo "Seeking Approval"
        timeout(time: 5, unit: "DAYS"){
        input message: 'Approve to production'
        }
      }
    }
 /*   stage('Build Docker Image') {
            steps {
                script {
                    def commit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    dockerImage = docker.build("${DOCKER_IMAGE}:${commit}", '.')
                }
            }
        }
    stage('Push Docker Image') {
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_cred') {
                        dockerImage.push("${commit}")
                        dockerImage.push('latest')
                }
            }
        }
    } */

    stage('Tomcat'){
      steps {
        echo 'Pushing to Tomcat'
        deploy adapters: [tomcat8(credentialsId: 'tomcat8_cred', path: '', url: 'http://172.31.88.139:7000/')], contextPath: null, war: 'target/*.war'
        echo 'Done'
      }
    }
  }
}
