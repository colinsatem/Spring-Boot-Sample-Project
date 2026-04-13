pipeline{
	 agent { 
	    label 'agent1' }
	tools {
	    maven 'Maven-3.9.14'
	    jdk    'jdk11' 
	}
	 environment {
        SONARQUBE_ENV = 'MySonarQube'
    }
	stages{
	    stage("cloning"){
	         step{
	         sh "echo latest version commited"
	         git "https://github.com/colinsatem/Spring-Boot-Sample-Project.git"
	         }
	    }
	    stage("Building .war"){
	        step{
	        echo "Building application"
            sh "mvn clean package -DskipTests"
	        }
	    }
	    stage("SonarQube Analysis"){
            steps {
                echo "Running SonarQube analysis"
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=myproject1 \
                        -Dsonar.projectName=myproject1 \
                        -Dsonar.host.url=http://34.207.150.179:9000/projects
                    """
                }
            }
	    }
	    stage("uplaod"){
	        steps {
                withCredentials([usernamePassword(credentialsId: 'Nexus-Jenkins', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        mvn deploy \
                        //-DaltDeploymentRepository=nexus::default::http://<nexus-ip>:8081/repository/maven-releases/ \
                        -DaltDeploymentRepository=nexus::default::http://100.52.252.210:8081/repository/maven-releases/ \
                        -Dusername=$USER \
                        -Dpassword=$PASS
                    """
                }
            }
	    }
	    stage("deployment"){
	        steps {
                echo "Deploying WAR to Tomcat"

                deploy(
                    adapters: [
                        tomcat9(
                            credentialsId: 'tomcat-creds2',
                            path: '',
                            url: 'http://100.54.221.9:8080'
                        )
                    ],
                    war: 'target/BankApplicationBackend.war'
                )
            }
	    }
	    stage("approval"){
	        steps {
                script {
                    input message: 'Approve deployment?', ok: 'Yes, deploy'
                }
           }
	    }
	    stage("deployprod"){}
	    stage("notification"){}
	}
	triggers{}
	post{
      always {}
      success {}
      failure {}	
	}
}
