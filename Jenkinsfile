#!groovy

pipeline{
    agent any
    stages {
        stage ('checkout'){
            steps{
                checkout scm
                sh 'echo "checking out repo"'
            }
        }

        stage ('build docker'){
            steps{
                dir("${env.WORKSPACE}/docker"){
                    sh "pwd"
                    sh 'echo "stopping docker containers"'
                    sh 'docker ps -aq | xargs -r docker stop;'
                    sh 'echo "killing docker containers"'
                    sh 'docker ps -aq | xargs -r docker rm;'
                    sh 'echo "building docker image"'
                    sh 'docker build . -t "initial_test"'
                }
            }
        }

        stage ('run docker'){
            steps{
                dir("${env.WORKSPACE}/docker"){
                    sh 'echo "trying to run docker"'
                    sh 'docker run -dit --name my_app  -p 8081:80 initial_test'
                }
            }
        }

        stage ('test docker'){
            steps{
                script{
                    def response = sh(returnStdout: true, script: 'curl localhost:8081')
                    def validated = false
                    if ( "${response}" =~ /Moshe/){
                        validated = true
                    }

                    echo "${validated}"
                }
            }
        }
    }

    post {
        always {
            echo 'I will always say Hello again!'
            // sg.addRecipient(javax.mail.Message.RecipientType.TO, new javax.mail.internet.InternetAddress("eyallei666@gmail.com"))
            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
            
        }
    }
}