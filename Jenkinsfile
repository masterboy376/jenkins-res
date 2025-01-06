pipeline {
    agent { 
        node {
            label 'Built-In'
            }
      }
    triggers {
        pollSCM '* * * * *'
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                cdmod +x dummy.sh
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                bash dummy.sh
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                sh '''
                echo "doing delivery stuff.."
                '''
            }
        }
    }
}
