pipeline
{
    agent any

    environment {
        BUILD_TS = " "
        DEVOPSUTILS = "https://github.com/idobry/devopsutils.git"
        SOURCE_BRANCH = sh(script: 'echo ${ref##*/}', returnStdout: true).trim()
        SOURCE_NAME = "${name}"
        REGISTRY = "https://registry-1.docker.io/v2/"
        VALUES_FILE = "charts/gitopsdemo/values.yaml"
        NEW_VALUES_FILE = "charts/gitopsdemo/new_values.yaml"
    }

    stages
    {
        stage('cleanup')
        {
            steps
            {
                script{
                    sh "if [ -d ".devopsutils" ]; then rm -Rf .devopsutils; fi"
                }                
            }
        }
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
                    dir('.devopsutils'){
                        git branch: SOURCE_BRANCH, credentialsId: 'idobry_github', url: DEVOPSUTILS
                        def values = readYaml file: "${VALUES_FILE}"
                        values.image.tag = "${SOURCE_BRANCH}-${env.BUILD_ID}"
                        writeYaml file: "${NEW_VALUES_FILE}", data: values
                        sh "cat ${NEW_VALUES_FILE}"
                        sh "rm ${VALUES_FILE} && mv ${NEW_VALUES_FILE} ${VALUES_FILE}"
                        sh "git commit -am 'update to version ${SOURCE_BRANCH}-${env.BUILD_ID}'"
                    } 
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
