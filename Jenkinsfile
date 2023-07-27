pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.3/bin:$PATH"
}
    stages {
        stage("build"){
            steps {
                echo "------------------- build started --------------"
                sh 'mvn clean deploy -Dmaven.test.skip-true'
                echo "------------------- build completed ------------"
            }
        }
        stage("test") {
            steps{
                echo "------------Unit test started -------------------"
                sh 'mvn surefire-report:report'
                echo "------------Unit test completed -------------------"
            }
        }
        stage('SonarQube analysis') {
            environment {
             scannerHome = tool 'valaxy-sonar-scanner'
        }
        steps {
    withSonarQubeEnv('valaxy-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
stage("Quality Gate"){
    steps {
        script {
    timeout(time: 2, unit: 'MINUTES') { //just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
        error "Pipeline aborded due to quality gate failure: ${qa.status}"
    }
    }
}
}
}
}
}