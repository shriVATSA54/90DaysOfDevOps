pipeline {
    agent any

    stages {
        stage('Setup Files') {
            steps {
                script {
                    // Check if Dockerfile exists, if not create it
                    if (!fileExists('Dockerfile')) {
                        echo 'Dockerfile does not exist, creating Dockerfile...'
                        writeFile file: 'Dockerfile', text: '''
                        # Use official Node.js runtime as a parent image
                        FROM node:16

                        # Set the working directory inside the container
                        WORKDIR /app

                        # Copy package.json and package-lock.json (if available)
                        COPY package*.json ./

                        # Install dependencies
                        RUN npm install

                        # Copy the rest of the application files
                        COPY . .

                        # Expose port 3000 (or any other port your app runs on)
                        EXPOSE 3000

                        # Command to run your app
                        CMD ["npm", "start"]
                        '''
                    } else {
                        echo 'Dockerfile already exists, skipping creation.'
                    }

                    // Check if requirements.txt exists (for Python projects), if not create it
                    if (!fileExists('requirements.txt')) {
                        echo 'requirements.txt does not exist, creating requirements.txt...'
                        writeFile file: 'requirements.txt', text: '''
                        flask
                        requests
                        numpy
                        '''
                    } else {
                        echo 'requirements.txt already exists, skipping creation.'
                    }

                    // You can also check and create package.json for Node.js (if needed)
                    if (!fileExists('package.json')) {
                        echo 'package.json does not exist, creating package.json...'
                        writeFile file: 'package.json', text: '''
                        {
                            "name": "90dayofdevops-app",
                            "version": "1.0.0",
                            "main": "index.js",
                            "dependencies": {
                                "express": "^4.17.1"
                            }
                        }
                        '''
                    } else {
                        echo 'package.json already exists, skipping creation.'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    bat "docker build -t 90dayofdevops-app:latest ."
                }
            }
        }

        // Add more stages for tests, deployment, etc.

        stage('Push to DockerHub') {
            steps {
                echo "Push image to Docker"
                withCredentials([usernamePassword(credentialsId: 'DockerHubCreds', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                    script {
                        try {
                            // Log into DockerHub
                            bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                            // Tag the Docker image
                            bat "docker image tag 90dayofdevops-app:latest ${env.dockerHubUser}/90dayofdevops-app:latest"
                            // Push the Docker image to DockerHub
                            bat "docker push ${env.dockerHubUser}/90dayofdevops-app:latest"
                        } catch (Exception e) {
                            error "Docker push failed: ${e.getMessage()}"
                        }
                    }
                }
            }
        }
    }
}
