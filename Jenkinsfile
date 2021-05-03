node {
    stage('Checkout SCM') {
        checkout scm
        sh "git rev-parse --short HEAD > commit-id"
        tag = readFile('commit-id').replace("\n", "").replace("\r", "")
        appname = "flask-alpine:"
        // registryHost name modified to use DockerHub
        registryHost = "wolfjde/" //"127.0.0.1:30400/"
        env.imageName = "${registryHost}${appname}${tag}"
        env.BUILD_TAG=tag
    }

    stage ('Build') {
        sh "docker build -t flask-alpine:1 ."
    }
    
    parallel(
        "stage qa":
        stage ('Qa') {
        def scannerHome = tool 'scanner';
        withSonarQubeEnv('SonarQube') {
            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=flask-alpine -Dsonar.sources=."
        }
      },
      "state cvsscan": 
        stage('cvsscan'){
        writeFile file: 'anchore_images', text: 'wolfjde/flask-alpine:2603d45'
        anchore name: 'anchore_images'

     }

    )    
  
}
