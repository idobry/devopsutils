pipeline
{
    agent any

    environment {
        BUILD_TS = " "
        DEVOPSUTILS = "https://github.com/idobry/devopsutils.git"    
        SOURCE_BRANCH = sh(returnStdout: true, script: 'echo ${ref##*/}').trim()
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
                script
                {
                    sh "if [ -d '.devopsutils' ]; then rm -Rf .devopsutils; fi"
                }                
            }
        }
        stage('get source')
        {
            steps
            {
                script
                {
                    dir('source')
                    {
                        git branch: SOURCE_BRANCH, credentialsId: 'idobry_github', url: '$clone_url'
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
                        def customImage = docker.build("idobry/gitopsdemo")
                        docker.withRegistry("${REGISTRY}", 'idobry-docker-hub-credentials') 
                        {
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
                script
                {
                    withCredentials([usernamePassword(credentialsId: 'idobry_github', usernameVariable: 'username', passwordVariable: 'password')])
                    {
                        sh "git clone http://$username:$password@github.com/idobry/devopsutils.git .devopsutils" 
                        sh "cd .devopsutils && git checkout -b ${SOURCE_BRANCH} origin/${SOURCE_BRANCH}"
                        def values = readYaml file: "${VALUES_FILE}"
                        values.image.tag = "${SOURCE_BRANCH}-${env.BUILD_ID}"
                        writeYaml file: "${NEW_VALUES_FILE}", data: values
                        sh "cat ${NEW_VALUES_FILE}"
                        sh "rm ${VALUES_FILE} && mv ${NEW_VALUES_FILE} ${VALUES_FILE}"
                        sh "git commit -am 'update to version ${SOURCE_BRANCH}-${env.BUILD_ID}' && git push origin/${SOURCE_BRANCH} HEAD:${SOURCE_BRANCH}"
                    }
/*
                    dir('.devopsutils')
                    {
                        //git branch: SOURCE_BRANCH, credentialsId: 'idobry_github', url: DEVOPSUTILS
                        //sh "git checkout ${SOURCE_BRANCH}"

                        sh "git clone https://idobry:liad171393@github.com/idobry/devopsutils.git"

                        def values = readYaml file: "${VALUES_FILE}"
                        values.image.tag = "${SOURCE_BRANCH}-${env.BUILD_ID}"
                        writeYaml file: "${NEW_VALUES_FILE}", data: values
                        sh "cat ${NEW_VALUES_FILE}"
                        sh "rm ${VALUES_FILE} && mv ${NEW_VALUES_FILE} ${VALUES_FILE}"
                        sh "git commit -am 'update to version ${SOURCE_BRANCH}-${env.BUILD_ID}' && git push canary"
                    } */
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
