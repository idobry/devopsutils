pipeline
{
    agent any

    environment {   
        SOURCE_BRANCH = sh(returnStdout: true, script: 'echo ${ref##*/}').trim()
        SOURCE_NAME = "${name}".toLowerCase()
        SOURCE_URL = "${clone_url}"
        COMMIT = "${commit}".getAt(0..6)
        REGISTRY = "https://registry-1.docker.io/v2/"
        TAG = "${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}"
    }

    stages
    {
        stage('get source')
        {
            steps
            {
                script
                {
                    dir('source')
                    {
                        git branch: SOURCE_BRANCH, credentialsId: 'idobry_github', url: SOURCE_URL
                    }                   
                }
            }
        }
        stage('docker build + push')
        {
            steps
            {
                script
                {
                    dir('source')
                    {
                        sh "echo building image for ${SOURCE_NAME}"
                        def customImage = docker.build("idobry/${SOURCE_NAME}")
                        def tag = "${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}"
                        docker.withRegistry("${REGISTRY}", 'idobry-docker-hub-credentials') 
                        {
                            customImage.push("${TAG}")
                        } 
                    }                   
                }
            }
        }
    }
}

//Groovy Helper Methods

@NonCPS
def updateVersion(String currentVersion){
    def split = currentVersion.split('\\.')
    split[2]=++Integer.parseInt(split[2])
    return split.join('.')
}

def msgToSlack(msg){
    slackSend color: '#287c28', message: msg
}
