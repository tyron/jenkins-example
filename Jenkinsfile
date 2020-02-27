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


        stage ('Deployment Stage') {
            steps {
                script {
                  timeout(time: 10, unit: 'MINUTES') {
                    input(id: "Deploy Gate", message: "Deploy ${params.project_name}?", ok: 'Deploy')
                  }
                }
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
