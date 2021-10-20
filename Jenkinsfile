pipeline {

    agent {
        node {
            label 'master'
        }
    }

    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Code Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/J-Phiz/spring-petclinic.git/']]
                ])
            }
        }

        stage(' Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                """
                withMaven {
                    sh "mvn -B test"
                }
            }
        }

        stage('Build Deploy Code') {
            when {
                branch 'main'
            }
            steps {
                sh """
                echo "Building Artifact"
                """
                withMaven {
                    sh "mvn -Dmaven.test.skip=true -DskipTests -B package"
                }

                sh """
                echo "Deploying Code"
                """
            }
        }
    }   
}
