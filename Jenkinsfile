pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = 'petclinic'
        BUILD_TAG = "latest"
        TENANT_ID = '7e8c858a-0db6-4a84-bf38-fa771c60387e'
        ACR_NAME = 'clinicjenkins'
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        FULL_IMAGE_NAME = "${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${BUILD_TAG}"
        RESOURCE_GROUP = 'Jenkins'
        CLUSTER_NAME = 'demo-eks'
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
    
        // stage('Trivy Scan') {
        //     steps {
        //        echo 'Running Trivy scan...'
        //        sh 'trivy fs --output trivy-report.txt --severity HIGH,CRITICAL .'  
        //     }
        // }
        // stage('Sonarqube Analysis') {
        //     environment {
        //         SCANNER_HOME = tool 'sonar-scanner'
        //     }
        //    steps {
        //     withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
        //         withSonarQubeEnv('sonarserver') {
        //             sh '''
        //                 ${SCANNER_HOME}/bin/sonar-scanner \
        //                 -Dsonar.projectName=PetClinic-app \
        //                 -Dsonar.projectKey=PetClinic \
        //                 -Dsonar.login=$SONAR_TOKEN \
        //                 -Dsonar.java.binaries=. \
        //                 -Dsonar.exclusions=**/trivy-report.txt
        //             '''
        //             }
        //         }
        //     }
        // }
        stage('Package'){
            steps{
                sh 'mvn package'
            }
        }
         
         stage('Build Docker Image'){
             

           steps{
            echo 'Building Docker image...'
             script{
               docker.build("${IMAGE_NAME}:${BUILD_TAG}")
            }

        }
    }
        stage('ACR login'){
            steps{
                script {
                    withCredentials([usernamePassword(credentialsId: 'azure-credentials', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                        echo "Logging into Azure Container Registry..."
                        sh '''
                            az login --service-principal -u "$AZURE_USERNAME" -p "$AZURE_PASSWORD" --tenant "$TENANT_ID"
                            az acr login --name clinicjenkins
                        '''
                    }
                }   
            }  
        }
        stage('Push Docker Image to ACR') {
            steps {
                script {
                    echo 'Pushing Docker image to Azure Container Registry...'
                    sh '''
                        docker tag ${IMAGE_NAME}:${BUILD_TAG} ${FULL_IMAGE_NAME}
                        docker push ${FULL_IMAGE_NAME}
                    '''
                }
            }
        }
    }
}

