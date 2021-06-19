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
                echo "TODO"
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
