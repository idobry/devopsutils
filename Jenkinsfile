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
                        def push_branch = sh(script: '${ref##*/}', returnStdout: true)
                        //git branch: '${push_branch}', credentialsId: 'idobry_github', url: '$clone_url'
                        git branch: '$ref', credentialsId: 'idobry_github', url: '$clone_url'
                        //def customImage = docker.build("idobry/gitopsdemo:${env.BUILD_ID}", "-f ./source")
                        //customImage.push() 
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
