node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("bryanwhyte/hellonode")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        sh 'echo "Create a tar of the container image for scanning"'
        sh "docker save -o hellonode.tar bryanwhyte/hellonode"
        
        app.inside {
            sh 'echo "Create Manifest of installed OS Packages"'
            sh 'dpkg-query -W > debian-packages.txt'
        }
              
    }
    
    stage('Nexus Lifecycle Evaluation') {
        nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: "${env.JOB_BASE_NAME}", iqStage: 'build', jobCredentialsId: '', iqScanPatterns: [[scanPattern: '**/hellonode.tar,debian-packages.txt']]
    }
        
    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. 
        docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }*/
    }
}
