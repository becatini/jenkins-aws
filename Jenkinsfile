pipeline {
    /* agent means which type of environment I will use to run this pipeline
        In this case, the agent will be my own server */
    agent any

    // stage means all steps to be executed on this pipeline
    stages {
        // this stage I will build my image
        stage ('Build Image') {
            steps {
                script {
                    //the line below is the dockerbuild command that will create the image according to the Dockerfile
                    //dockerapp is a variable I will use to build the image
                    //web-live-app is the image name. The version will be a jenkins variable that will use the pipeline ID execution                    
                    dockerapp = docker.build("becatini/web-live-app:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
                }
            }
        }

        //this stage will push the image to the registry
        stage ('Push Image') {
            steps {
                script {
                    //docker.withRegistry is the authentication process to my dockerhub
                    //mydockerhub is the credential ID that I will specify on Jenkins credentials
                    docker.withRegistry('https://registry.hub.docker.com', 'mydockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        /*stage ('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withAWS(credentials: 'jenkins-credential', region: 'us-east-1') {
                    sh 'aws eks update-kubeconfig --name live-eks'
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }

            }
        }*/
    }
}