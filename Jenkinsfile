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
        stage('update chart')
        {
            steps
            {
                script{
                    sh "git checkout ${SOURCE_BRANCH}"
                    values = readYaml file: "charts/gitopsdemo/values.yaml"
                    //modify
                    mydata.image.tag = "b"
                    writeYaml file: 'charts/gitopsdemo/values.yaml', data: mydata
                    sh "cat charts/gitopsdemo/values.yaml"
                    sh "git add charts/gitopsdemo/values.yaml && git commit -m 'update to version ${SOURCE_BRANCH}-${env.BUILD_ID}'"
                }

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
