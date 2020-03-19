pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install -Dlicense.skip=true'
        echo 'Build step complete'
      }
    }

    stage('Testing') {
      environment {
        pipelinetest = ''
      }
      parallel {
        stage('pipeline test') {
          steps {
            echo "The tester is ${TESTER}"
            sleep 10
          }
        }

        stage('Print Build Number') {
          environment {
            TESTER = 'GAYATRI'
          }
          steps {
            echo "This is build number ${BUILD_ID}"
            sleep 20
          }
        }

      }
    }

    stage('JFrog Push') {
      steps {
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

        echo 'Starting Jfrog push'
        echo 'Finished JFrog push'
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
        echo 'Deployment Complete'
      }
    }

  }
  environment {
    pipeline = ''
  }
}