pipeline {
    agent any

    parameters {
        string(name: 'Email', defaultValue: '', description: 'Email. Ex: example@hdiexample.com.co')
        choice(name: 'Stage', choices: ['dev', 'nonprod', 'uat', 'prod'], description: 'Stage or Enviroment to Excute. Ex: dev')
        choice(name: 'Action', choices: ['SELECT'], description: 'Select the action to execute. Ex: SELECT')
        string(name: 'Attributes', defaultValue: '*', description: 'Select attributes to display. (*) for all attributes, if you want to specify placing the attributes separated by (,). Example: id, name')
        string(name: 'TableName', defaultValue: '', description: 'Write the Table Name. Example: MyTable')
        string(name: 'Conditional', defaultValue: 'WHERE ', description: 'Write condition. Example: WHERE id = 1;')
    }

    environment {
        USER_INPUT = null
    }

    options {
        timestamps()
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Execute Query PartiQL') {
            when {
                expression {
                    return params.Stage == "prod" || params.Stage == "nonprod" || params.Stage == "uat"
                }
            }
            steps {
                script {
                                        // Build the dynamic PartiQL query using the parameters
                    def action = params.Action
                    def attributes = params.Attributes
                    def conditional = params.Conditional
                    
                    // Define the table name (you can adjust this or make it a parameter if needed)
                    def tableName = params.TableName

                    // Build the PartiQL query
                    def partiqlQuery = "${action} ${attributes} FROM ${tableName} ${conditional};"

                    // Show the generated PartiQL query
                    echo "Generated PartiQL query: ${partiqlQuery}"

                    USER_INPUT = input message: "Do you approve the execution of the following query?\n\n${partiqlQuery}", 
                                        ok: "Execute",
                                        submitterParameter: 'userSubmitter'
                    sh """
                    echo USER_INPUT: ${USER_INPUT}
                    echo Executing...
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            script {
                // notificationSuccess()
            }
        }
        failure {
            script {
                // notificationError()
            }
        }    
    }
}

def notificationSuccess() {
    def subject = "${Email}"
    def bodyText = ""
    subject = "[${params.Stage}] - Released Success in ${env.JOB_NAME} - ${params.ArtifactoryId}"
    bodyText = """
    Hi there!!

    Deployment successful! Artifactory ID: ${params.ArtifactoryId} to the ${params.Stage} environment.

    See job here: ${env.BUILD_URL}

    See log here: ${env.BUILD_URL}consoleText
    """
    echo "Sending email with subject '${Email}' and content:\n${bodyText}"
}

def notificationError() {
    def subject = "${Email}"
    def bodyText = ""
    subject = "[${params.Stage}] - Released Error in ${env.JOB_NAME} - ${params.ArtifactoryId}"
    bodyText = """
    Hi there!!

    Deployment successful! Artifactory ID: ${params.ArtifactoryId} to the ${params.Stage} environment. 

    See job here: ${env.BUILD_URL}

    See log here: ${env.BUILD_URL}consoleText
    """
    echo "Sending email with subject '${Email}' and content:\n${bodyText}"
}