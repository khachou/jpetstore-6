pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install -Dlicense.skip=true'
        echo 'Staring maven clean build'
        echo 'Finished maven package build'
      }
    }

    stage('Testing') {
      parallel {
        stage('Sonarqube Test') {
          steps {
            echo 'Starting Sonarqube test'
            sh 'mvn sonar:sonar -Dsonar.host.url=http://<IP address>:8081 -Dlicense.skip=true'
          }
        }

        stage('Print tester Identity') {
          steps {
            echo "The tester is $TESTER"
            sleep 10
          }
        }

        stage('Print Build Number') {
          steps {
            echo "This is build number ${BUILD_ID}"
            sleep 20
          }
        }

      }
    }

    stage('JFrog push') {
      steps {
        echo 'JFrog push starting'
        script {
          def server = Artifactory.server "artifactory"
          def buildInfo = Artifactory.newBuildInfo()
          def rtMaven = Artifactory.newMavenBuild()
          rtMaven.tool = 'maven'
          rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
          buildInfo = rtMaven.run pom: 'pom.xml', goals: "clean install -Dlicense.skip=true"
          buildInfo.env.capture = true
          buildInfo.name = 'jpetstore-6'
          server.publishBuildInfo buildInfo
        }

        echo 'JFrog push completed'
      }
    }

    stage('Deploy prompt') {
      steps {
        input 'Deploy to Production?'
      }
    }

    stage('Deployment') {
      steps {
        echo 'Starting Deployment'
        echo 'Deployment Completed'
      }
    }

  }
  tools {
    maven 'maven'
  }
  environment {
    TESTER = 'Bamba'
  }
}