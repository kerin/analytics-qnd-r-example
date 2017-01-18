node {
    def DEPLOY_DIR = "./deploy"
    def APP_NAME = sh(
        script: "echo ${env.JOB_NAME} | tr '[:upper:]' '[:lower:]' | tr -s '_ ' '-' |cut -c1-15",
        returnStdout: true
    ).trim()

    stage('Checkout') {
        checkout scm
        // Incredibly, Jenkins Pipeline scripts have no access to git variables,
        // so they're handled manually here.
        //
        // Declaring the vars with 'env.FOO' makes them accessible outside of this
        // scope (in a different stage), but they're not actually env vars from
        // a shell script's point of view. Groovy is fun!
        env.GIT_SHA = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
        env.GIT_URL = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()
    }

    // stage("Build") {
    //     def Img = docker.build "kerin/analytics-qnd-r-example:latest"
    // }

    // stage('Prepare') {
    //     sh('apt-get install -y gettext')
    // }

    // stage('Test') {
    //     echo "${APP_NAME}"
    //     sh "/usr/local/bin/kubectl get pods"
    // }

    stage ('Build') {
        echo "${GIT_SHA}"
        echo "${GIT_URL}"

        sh "mkdir build"
        sh """
        for f in ${DEPLOY_DIR}/*.y*ml
        do
            APP_NAME=${APP_NAME}
            GIT_SHA=${GIT_SHA}
            GIT_URL=${GIT_URL}
            envsubst < \$f > build/\$(basename \$f);
        done
        """
    }

    stage ('Deploy') {
        sh "kubectl apply -f build/"
        sh "kubectl rollout status deployment/${APP_NAME}"
    }

    // stage 'Deploy' {
    //     sh("mkdir build")
    //     sh("for f in ${deploy_dir}/*.y*ml; do envsubst < $f > build/$(basename $f); done")
    //     sh("kubectl apply -f build/")
    //     sh("kubectl rollout status deployment/${APP_NAME}")
    // }
}
