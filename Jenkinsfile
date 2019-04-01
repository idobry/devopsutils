pipeline
{
    agent any

    environment {
        BUILD_TS = " "
    }

    stages
    {
        stage('step1')
        {
            steps
            {
                echo '$html_url step1'
                sh "echo $html_url"
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
