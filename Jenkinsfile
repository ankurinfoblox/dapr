@Library('jenkins.shared.library') _

pipeline {
  agent {
    label 'ubuntu_docker_label'
  }
  tools {
    go "Go 1.16"
  }
    options {
        checkoutToSubdirectory('src/github.com/infobloxopen/dapr')
  }
  environment {
    GOPATH = "$WORKSPACE"
    DIRECTORY = "src/github.com/infobloxopen/dapr"
    DOCKER_IMAGE = "infoblox/dapr"
    
  }
  stages {
    stage("Setup") {
      steps {
        prepareBuild()
      }
    }
   stage("Test") {
      steps {
        sh "cd $DIRECTORY && make test"
      }
    }
   stage("build-and-archive-binaries-darwin-amd64") {
      steps {
        sh "cd $DIRECTORY && make tidy && make release GOOS='darwin' GOARCH='amd64' "
      }
     	  
	  
    }
   stage("build-and-archive-binaries-linux-arm64"){
        steps {
          sh "cd $DIRECTORY && make tidy && make release GOOS='linux' GOARCH='arm64' "
        }

      }
    stage("build-and-archive-binaries-linux-amd64"){
         steps {
          sh "cd $DIRECTORY && make tidy && make release GOOS='linux' GOARCH='amd64' "
        }
      }
    stage("Build-And-Push-Docker") {
       steps {
        withDockerRegistry([credentialsId: "dockerhub-bloxcicd", url: ""]) {
          sh "cd $DIRECTORY && make docker-push USERNAME='Jenkins' GOOS='darwin' GOARCH='amd64'  && make docker-push-arm GOOS='linux' GOARCH='arm64' && make docker-push GOOS='linux' GOARCH='amd64' "
          
        }
      }
    }
  
  }
  post {
    success {
      finalizeBuild()
    }
    cleanup {
      sh "cd $DIRECTORY && make clean"
    }
  }
}
