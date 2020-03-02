def userInput() {
    def userInput = false
    def didTimeout = false
    try {
        timeout(time: 2, unit: 'MINUTES') {
            userInput = input(
                id: 'Proceed1', message: 'Was this successful?', parameters: [
                [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']
                ])
        }
    } catch(err) { // input false
        def user = err.getCauses()[0].getUser()
        if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
            didTimeout = true
        } else {
            echo "Aborted by: [${user}]"
        }
    }
    return userInput
}

pipeline {  
    agent any

    stages {
        stage ('Compile Stage') {

            steps {
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn clean compile'
                }
            }
        }

        stage ('Testing Stage') {

            steps {
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn test'
                }
            }
        }

        stage ('Artifact publish') {
            steps {
             rtUpload (
                serverId: 'artifactory',
                spec: '''{
                      "files": [
                        {
                          "pattern": "jenkins-example-*.*",
                          "target": "libs-snapshot-local/jenkins-example/"
                        }
                     ]
                }'''
              )
            }
        }
        
        stage('Deployment Approval') {
            steps {
                script {
                    approved = userInput()
                }
            }
        }

        stage ('Deployment Stage') {
            when { expression { return approved } }
            steps {
                /*
                script {
                  timeout(time: 2, unit: 'MINUTES') {
                    input(id: "Deploy Gate", message: "Deploy ${env.JOB_NAME}?", ok: 'Deploy')
                  }
                }
                */
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
