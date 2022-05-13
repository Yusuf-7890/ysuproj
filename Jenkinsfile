    pipeline {
        environment {
            registry = "mohammadathar/cryptoapp"
            registryCredential = 'dockerhub_credentials'
            dockerImage = ''
        }
        agent any
        stages {
            stage('Build Application') {
                steps {
                    sh 'yarn'
                    sh 'npm i'
                    sh 'yarn build'
                }
                post {
                    success {
                        echo "Now Archiving the Artifacts...."
                        archiveArtifacts artifacts: '**/build/*'
                    }
                }
            }
            stage('Docker Image of the app'){
                steps{
                    copyArtifacts projectName: env.JOB_NAME, filter: "build/*", selector: specific(env.BUILD_NUMBER);
                    script {
                        dockerImage = docker.build env.registry+'${BUILD_NUMBER}'
                        }
                }
                
            }
            
            stage('pushing our app image to docker hub') {
                steps{
                    script {
                        docker.withRegistry( '', env.registryCredential ) {
                            dockerImage.push()
                            }
                        }
                    }
                }
                stage('Deploy to staging server'){
                steps{
                    sh 'docker rm -f $(docker ps -a -q)'
                    sh 'docker container run -d -p 80:80 ${registry}:${BUILD_NUMBER}'
                }
            }
        }
            stage('running ansible playbook to deploy on production environment') {
                steps{
                    build job:'Ansible deployment'
                        }
                    }
                }
        }
    }
