pipeline {
    environment {
        registry = "domingocooler/homelab-spinnaker"
        registryCredential = "homelab01-dockerhub"
        dockerImage = ""
  }
    agent any
    tools { 
        maven 'mvn361' 
        jdk 'jdk8'
  }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
      }
    }
        stage('Login') {
            steps {
                sh 'echo $HOMELAB01-DOCKERHUB_CREDENTIALS_PSW | docker login -u $HOMELAB01-DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
        stage ('Code Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true package'
      }
            post {
                success {
                    junit 'target/surefire-reports /**/*.xml'
        }
      }
    }
        stage ('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build (registry + ":${BUILD_NUMBER}",
          "--build-arg JARFILE=person-0.0.1-SNAPSHOT.jar .")
        }
      }
    }
        stage ('Docker Publish') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
          }
        }
      }
    }
        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
    post {
       always {
         sh 'docker logout'
    }
  }
}
