pipeline {
    agent any
    tools {
        nodejs 'node20'
       // dockerTool 'docker'
    }
    environment {
        SONARQUBE_SERVER_ENV='sonarqubeserver'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_CREDENTIALS_ID ="docker-creds"
        DOCKER_IMAGE = "dataquaintacademy/dhl_repo"
        IMAGE_TAG ="${env.BUILD_NUMBER}"
    }

    stages {
        stage('CheckoutSourceCode') {
            steps {
                git branch: 'main', url: 'https://github.com/Quantumvector2026/dhl.git'
                slackSend channel: 'teamsamurai', message: 'Checkout Successful'
                mail bcc: 'projects2488@gmail.com', body: 'Checkout source code successful', cc: 'projects2488@gmail.com', from: '', replyTo: '', subject: 'Build successful', to: 'projects2488@gmail.com'
            }
        }
        stage("Environment Setup")
        {
            steps
            {
                echo "Checking workspace and tools"
                sh 'node --version'
                sh 'npm --version'
            //    sh 'docker --version'
            }
        }
      
     
        
        stage("SonarqubeStaticScan")
        {
            steps
            {
                script {
                    def scannerHome =tool 'sonar-scanner'
                    withSonarQubeEnv(SONARQUBE_SERVER_ENV) {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
                timeout(time: 10, unit: 'MINUTES'){
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline Aborted: ${qg.status}"
                        }
                    }
                }
            }
        }
        
stage(Security_scan_trivy_filesystem)
{
    steps
    {
        echo "Scan the project workspace for vulnerabilities"
        sh 'trivy fs --exit-code 0 --severity HIGH,CRITICAL --format table .'
    }
}
    }
/*
stage("Build_Docker_image")
{
    steps
    {
        echo "Building docker image: ${DOCKER_IMAGE}:${IMAGE_TAG}...."
        sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
        sh "docker tag ${DOCKER_IMAGE}:${IMAGE_TAG} ${DOCKER_IMAGE}:latest"
    }
}
}
*/
    post
    {
        failure
        {
            slackSend channel: 'teamsamurai', message: 'Pipeline Failed'
            mail bcc: 'projects2488@gmail.com', body: 'Pipeline Failed', cc: 'projects2488@gmail.com', from: '', replyTo: '', subject: 'Pipeline Failed!!', to: 'projects2488@gmail.com'
        }
    }
}
