pipeline {
    agent any
    environment {
        WEBSITE_URL = 'https://zero.webappsecurity.com'
        REPO_URL = 'https://github.com/matiya/spring-boot-kubernetes/'
        ZAP_URL = 'http://localhost'
        ZAP_PORT = '8091'
        ZAP_API_KEY = '94r3b66ks38fu3mnael7t88qip'
    }
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
        stage('SonarQube Scanner') {
            tools {
                jdk 'java11'
            }
            steps{
                script {
                    def scannerHome = tool 'sonarqube-scanner';
                    withSonarQubeEnv('sonar-server'){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=springboot-curso-devsecops -Dsonar.sources=src/main/java -Dsonar.java.binaries=target/classes -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=04a1d38da9b0bc8a33a993119bd2c3dc7a86691a"
                    }
                }
            }
        }
        stage ('SCA') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
                dependencyCheckPublisher failedNewCritical: 5, failedTotalCritical: 10, pattern: 'terget/dad.xml', unstableNewCritical: 3, unstableTotalCritical: 5
            }
        }

        stage ('Container scan with Trivy'){
            steps{
                script{
                    sh 'docker run --rm -v /home/jenkins4docker/trivy-cache/:/root/.cache aquasec/trivy:latest repo --format template --template "@contrib/html.tpl" -o report.html ${REPO_URL}'
                }
            }
        }
        stage("Zap Security Scan"){
            steps{
                    sh ''' set -e
                    echo ${WEBSITE_URL}

                    virtualenv3 venv
                    source venv/bin/activate
                    pip install --upgrade pip
                    pip install --upgrade zapcli

                    zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} status -t 30
                    zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} open-url "${WEBSITE_URL}"
                    zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} active-scan --scanners all --recursive "${WEBSITE_URL}"
                    zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} report -o reporte-zap.html -f html
                    # zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} report -o activescan.pdf -f pdf
                    zap-cli --zap-url ${ZAP_URL} -p ${ZAP_PORT} alerts -l Low '''

            }
        }
    }
}
