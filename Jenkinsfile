pipeline
{
    agent any

    environment {
        BUILD_TS = " "
        DEVOPSUTILS = "github.com/idobry/devopsutils.git"    
        SOURCE_BRANCH = sh(returnStdout: true, script: 'echo ${ref##*/}').trim()
        SOURCE_NAME = "${name}"
        COMMIT = "${commit}".getAt(0..4)
        REGISTRY = "https://registry-1.docker.io/v2/"
        VALUES_FILE = "charts/gitopsdemo/values.yaml"
        CHART_FILE = "charts/gitopsdemo/Chart.yaml"
        NEW_VALUES_FILE = "charts/gitopsdemo/new_chart.yaml"
        NEW_CHART_FILE = "charts/gitopsdemo/new_values.yaml"
        TAG = "${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}"
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
                            customImage.push("${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}")
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
                   dir('.devopsutils')
                   {
                         withCredentials([usernamePassword(credentialsId: 'github-agent-token2', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])
                        {
                            stage('clone devopsutils'){
                                sh "git clone https://$USERNAME:$PASSWORD@$DEVOPSUTILS ."
                                sh "git checkout $SOURCE_BRANCH"
                            }
                            stage('edit value.yaml file'){
                                def values = readYaml file: "${VALUES_FILE}"
                                values.image.tag = "${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}"
                                writeYaml file: "${NEW_VALUES_FILE}", data: values
                                sh "rm ${VALUES_FILE} && mv ${NEW_VALUES_FILE} ${VALUES_FILE}"
                                sh "git add ${VALUES_FILE}"
                            }
                            stage('edit Chart.yaml file'){
                                def chart = readYaml file: "${CHART_FILE}"
                                def old_chart_version = chart.version
                                chart.version = updateVersion(old_chart_version)
                                writeYaml file: "${NEW_CHART_FILE}", data: chart
                                sh "rm ${CHART_FILE} && mv ${NEW_CHART_FILE} ${CHART_FILE}"
                                sh "git add ${CHART_FILE}"
                            } 
                            stage('commit and push'){
                                sh "git commit -m 'update to version ${SOURCE_BRANCH}-${COMMIT}-${env.BUILD_ID}'"
                                sh "git push"
                            }
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
