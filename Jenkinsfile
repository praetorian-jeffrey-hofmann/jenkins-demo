import groovy.json.JsonOutput

/**
 * Notify the Atomist services about the status of a build based from a
 * git repository.
 */
def notifyAtomist(String workspaceIds, String buildStatus, String buildPhase="FINALIZED") {
    if (!workspaceIds) {
        echo 'No Atomist workspace IDs, not sending build notification'
        return
    }
    def payload = JsonOutput.toJson(
        [
            name: env.JOB_NAME,
            duration: currentBuild.duration,
            build: [
                number: env.BUILD_NUMBER,
                phase: buildPhase,
                status: buildStatus,
                full_url: env.BUILD_URL,
                scm: [
                    url: env.GIT_URL,
                    branch: env.COMMIT_BRANCH,
                    commit: env.COMMIT_SHA
                ]
            ]
        ]
    )
    workspaceIds.split(',').each { workspaceId ->
        String endpoint = "https://webhook.atomist.com/atomist/jenkins/teams/${workspaceId}"
        sh "curl --silent -X POST -H 'Content-Type: application/json' -d '${payload}' ${endpoint}"
    }
}

pipeline {
    agent any
    stages {
        stage('Notify') {
            steps {
                echo 'Sending build start...'
                notifyAtomist(ANCDLPDCO, 'STARTED', 'STARTED')
            }
        }

        stage('Set version') {
            steps {
                echo 'Setting version...'
            }
        }

        stage('Build, Test, and Package') {
            steps {
                echo 'Building, testing, and packaging...'
            }
        }
    }
    post {
        always {
            echo 'Post notification...'
            notifyAtomist("ANCDLPDCO", currentBuild.currentResult)
        }
    }
}