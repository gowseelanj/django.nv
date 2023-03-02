pipeline {
    agent any
stages {
        stage("build") {
            steps {
                echo"This is a build step"}
        }
        stage("test") {
            steps {
                echo"This is a test step"}
        }
        stage("integration") {
            steps {
               sh 'echo"This is an integration step" >output.txt' }
            }
        }
       # create a file named output.txt and save it as an artifact
       post {
          always {
               archiveArtifacts artifacts: 'output.txt', onlyIfSuccessful: true
             }
       }

        stage("prod") {
            steps {
                input "Deploy to production?"echo"This is a deploy step."}
        }
    }
}
