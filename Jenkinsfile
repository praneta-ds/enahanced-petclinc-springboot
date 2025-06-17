pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = 'app-image'
        BUILD_TAG = "latest"
    //TENANT_ID = '9288e819-a217-4590-8b41-5088c8ee0457'
    //ACR_NAME = 'dockerregnodejss'
    //ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
    //FULL_IMAGE_NAME = "${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${BUILD_TAG}"
    // RESOURCE_GROUP = 'demo-rg'
    //CLUSTER_NAME = 'demo-aks'
    }
    stages {
        stage('Checkout From Git') { 
            steps {
                git branch: 'prod', url: 'https://github.com/praneta-ds/enahanced-petclinc-springboot.git'
            }
        }
        // stage('Maven Compile') { 
        //     steps {
        //         echo 'This Maven Compile Stage'
        //         sh 'mvn compile'
        //     }
        // }
        // stage('Maven Test') { 
        //     steps {
        //         echo 'This Maven Test Stage'
        //         sh 'mvn test'
        //     }
        // }
    
        stage('Trivy Scan'){
            steps{
               echo 'Running Trivy scan...'
               sh 'trivy fs --output trivy-report.txt --severity HIGH,CRITICAL .'  
            }
        }
        stage('Sonarqube Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                    //withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]){
                        withSonarQubeEnv('sonarserver'){
                        /* groovylint-disable-next-line GStringExpressionWithinString */
                        sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectName=PetClinic-app \
                        -Dsonar.projectKey=PetClinic \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-report.txt
                    '''
                    }
                //}
            }
        }
        // stage('Package'){
        //     steps{
        //         sh 'mvn package'
        //     }
        // }
        // stage('Build Docker Image'){
        //     steps{
        //         echo 'Building Docker image...'
        //         script{
        //             docker.build("${IMAGE_NAME}:${BUILD_TAG}")
        //         }

    //     }
    // }
    }
}

