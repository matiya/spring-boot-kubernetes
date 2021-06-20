pipeline {
    agent any

    tools {
        maven 'Maven'
    }
    stages {
        stage ('Info') {
            steps {
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
	    stage ('Compile') {
            tools {
          	    jdk 'java11'
              }
            steps {
                 sh 'mvn clean compile -e'
            }
        }
      stage ('Test') {
            tools {
          	    jdk 'java11'
              }
            steps {
                 sh 'mvn clean test -e'
            }
      }

        stage('SAST') {
           tools {
          	    jdk 'java11'
              }
           steps{
                script {
                  def scannerHome = tool 'sonarqube-scanner';
                  withSonarQubeEnv('sonar-server'){
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=springboot-curso-devsecops -Dsonar.sources=target/ -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=04a1d38da9b0bc8a33a993119bd2c3dc7a86691a"
                  }
                    }
                }
           }

        stage ('SCA') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

    }
}
