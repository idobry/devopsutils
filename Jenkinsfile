pipeline
{
    agent any

    environment {
        BUILD_TS = " "
        SOURCE_BRANCH = sh(script: 'echo ${ref##*/}', returnStdout: true).trim()
        SOURCE_NAME = "${name}"
        REGISTRY = "https://registry-1.docker.io/v2/"
    }

    stages
    {
        stage('get source')
        {
            steps
            {
                script{
                    dir('source'){
                        //def source_branch = sh(script: 'echo ${ref##*/}', returnStdout: true)
                        //source_branch = source_branch.trim()
                        git branch: SOURCE_BRANCH, credentialsId: 'idobry_github', url: '$clone_url'
                    }                   
                }
            }
        }
        stage('docker build + push')
        {
            steps
            {
                script{
                    dir('source'){
                        sh "echo building image for ${SOURCE_NAME}"
                        def customImage = docker.build("idobry/gitopsdemo")
                        docker.withRegistry("${REGISTRY}", 'idobry-docker-hub-credentials') {
                            customImage.push("${SOURCE_BRANCH}-${env.BUILD_ID}")
                        } 
                    }                   
                }
            }
        }
        stage('step3')
        {
            steps
            {
                echo 'step3'
            }
        }
        stage('step4')
        {
            steps
            {
                echo 'step4'
            }
        }

    }
}
