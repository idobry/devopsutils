pipeline
{
    agent any

    environment {
        BUILD_TS = " "
    }

    stages
    {
        stage('get source')
        {
            steps
            {
                script{
                    dir('source'){
                        def source_branch = sh(script: 'echo ${ref##*/}', returnStdout: true)
                        source_branch = source_branch.trim()
                        git branch: source_branch, credentialsId: 'idobry_github', url: '$clone_url'
                        def customImage = docker.build("idobry/gitopsdemo")
                        docker.withRegistry('https://registry-1.docker.io/v2/', 'idobry-docker-hub-credentials') {
                            def tag = "${source_branch}-${env.BUILD_ID}"
                            customImage.push("${tag}")
                        } 
                    }                   
                }
            }
        }
        stage('step2')
        {
            steps
            {
                echo 'step2'
                sh "echo $ref"
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
