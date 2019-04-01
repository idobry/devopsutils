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
                        //git branch: '${source_branch}', credentialsId: 'idobry_github', url: '$clone_url'
                        sh "echo $source_branch"
                        git branch: 'master', credentialsId: 'idobry_github', url: '$clone_url'
                        //sh "git checkout $source_branch"
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
