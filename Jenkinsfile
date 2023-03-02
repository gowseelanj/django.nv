pipeline {
    agent anyoptions {
        gitLabConnection('gitlab')
    }
stages {
        stage("build") {
            agent {
                docker {
                    image 'python:3.6'args '-u root'}
            }
            steps {
                sh """
                pip3 install --user virtualenv
                python3 -m virtualenv env
                . env/bin/activate
                pip3 install -r requirements.txt
                python3 manage.py check
                """}
        }
        stage("test") {
            agent {
                docker {
                    image 'python:3.6'args '-u root'}
            }
            steps {
                sh """
                pip3 install --user virtualenv
                python3 -m virtualenv env
                . env/bin/activate
                pip3 install -r requirements.txt
                python3 manage.py test taskManager
                """}
        }
        stage("oast-frontend") {
            agent {
                docker {
                    image 'node:alpine3.10'args '-u root'}
            }
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh """
                    npm install
                    npm install -g retire
                    retire --outputformat json --outputpath retirejs-report.json --severity high
                    """}
            }
        }
        stage("integration") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step."sh "exit 1"}
            }
        }
        stage("prod") {
            steps {
                input"Deploy to production?"echo "This is a deploy step."}
        }
    }
    post {
        failure {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'failed')
        }
        unstable {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'failed')
        }
        success {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'success')
        }
        aborted {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'skipped')
        }
        always {
            archiveArtifacts artifacts: '*.json', fingerprint: true
            deleteDir()                     // clean up workspace
        }
    }
}
