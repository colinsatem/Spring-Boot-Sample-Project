pipeline {
    agent { label 'agent1' }

    tools {
        maven 'Maven-3.9.14'
    }

    environment {
        SONARQUBE_ENV = 'MySonarQube'
    }

    stages {

        stage('Clone') {
            steps {
                echo "Cloning repository"
            }
        }

        stage('Build WAR') {
            steps {
                echo "Building application"
                sh "mvn clean package -DskipTests"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Running SonarQube analysis"
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=myproject \
                        -Dsonar.projectName=myproject \
                        -Dsonar.host.url=http://13.220.59.57:9000
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying WAR to Tomcat"

                deploy(
                    adapters: [
                        tomcat9(
                            credentialsId: 'tomcat-creds2',
                            path: 'demo',
                            url: 'http://34.229.81.67:8080'
                        )
                    ],
                    war: 'target/demo-0.0.1-SNAPSHOT.war'
                )
            }
        }
    }
}

