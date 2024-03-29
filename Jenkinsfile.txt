node {
    def app
    stage('Clone repository') {
        checkout scm
    }
    stage('Build image') {
        app = docker.build("${env.BRANCH_NAME}-${env.BUILD_NUMBER}")
    }
    stage('Push image') {   
        docker.withRegistry('https://index.docker.io/v1/', 'my-new-dockerhub-credentials') {
            app.push("${env.BRANCH_NAME}-${env.BUILD_NUMBER}")
            app.push("${env.BRANCH_NAME}-latest")
            // signal the orchestrator that there is a new version
        }
    }
}
