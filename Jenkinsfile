pipeline {
    
    agent any
     tools{
        maven 'maven-3'
    }
    environment {
        registry = '327742897043.dkr.ecr.ap-south-1.amazonaws.com/hellodatarepo'
        registryCredential = 'jenkins-ecr-login-credentials'
        dockerimage = ''
    }
    stages{
        stage("Checkout the project") {
           steps{
               git branch: 'main', url: 'https://github.com/midathanasiva/sivanov26.git'
           } 
        }
        stage("Build the package"){
            steps {
                sh 'mvn clean package'
            }
            
            
        }
        stage("Sonar Quality Check"){
		steps{
		    script{
		     withSonarQubeEnv(installationName: 'sonar-9', credentialsId: 'jenkins-sonar-token') {
		     sh 'mvn sonar:sonar'
	    	}
	    	timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
         }
	    	}
    }
}
}   
stage('Building the Image') {
        steps {
            script {
            dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
    }
    }
    stage ('Deploy the Image to Amazon ECR') {
       steps {
           script {
           docker.withRegistry("http://" + registry, "ecr:ap-south-1:" + registryCredential ) {
           dockerImage.push()
     }
}
}
}
}
post {
        success {
            mail bcc: '', body: 'Pipeline build successfully', cc: '', from: 'sivasunshine2@gmail.com', replyTo: '', subject: 'The Pipeline success', to: 'sivasunshine2@gmail.com'
        }
        failure {  
            mail bcc: '', body: 'Pipeline build not success', cc: '', from: 'sivasunshine2@gmail.com', replyTo: '', subject: 'The Pipeline failed', to: 'sivasunshine2@gmail.com'
         } 
    }
}

