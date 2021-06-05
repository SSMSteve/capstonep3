pipeline {
    environment {
        // This registry is important for removing the image after the tests
        registry = "slord0001/capstonep3"
    }
    agent any
    stages {
        stage("Build, Test and push to registry") {
            steps {
                script {
                    // Building the Docker image
                    dockerImage = docker.build("$registry")

                    try {
                        dockerImage.inside() {
                            // Extracting the PROJECTDIR environment variable from inside the container
                            def PROJECTDIR = sh(script: 'echo \$PROJECTDIR', returnStdout: true).trim()

                            // Copying the project into our workspace
                            sh "cp -r '$PROJECTDIR' '$WORKSPACE'"

                            // Running the tests inside the new directory
                            dir("$WORKSPACE$PROJECTDIR") {
                                sh "npm test"
                            }
                        }

                    } finally {
                        // push image
                        docker.withRegistry('https://registry.hub.docker.com', 'b5a58bfa-3753-4818-a8cc-e4be44d06a7a') {
                            app.push("$registry:$BUILD_NUMBER")
                            app.push("latest")
                        } 
                        echo "Trying to Push Docker Build to DockerHub"

                        // Removing the docker image
                        sh "docker rmi $registry:$BUILD_NUMBER"
                                                echo "Removing image" + "registry.hub.docker.com/$registry:$BUILD_NUMBER"
                        sh "docker rmi registry.hub.docker.com/$registry:$BUILD_NUMBER"
                        echo "Removing image" + "registry.hub.docker.com/$registry:latest"
                        sh "docker rmi registry.hub.docker.com/$registry:latest"
                    }
                }
            }
        }
    }
}