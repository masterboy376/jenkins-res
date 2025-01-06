pipeline {
    agent { 
        node {
            label 'built-in'
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
                chmod +x dummy.sh
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
