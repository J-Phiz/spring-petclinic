/*pipeline {

    agent {
        node {
            label 'master'
        }
    }
    tools {
        maven 'Maven'
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

        stage('Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                mvn -B test
                """
            }
        }

        stage('Build Code') {
            steps {
                sh """
                echo "Building Artifact"
                mvn -Dmaven.test.skip=true -DskipTests -B package
                """
            }
        }

        stage('Deploy Code') {
            when {
                branch 'main'
            }
            steps {
                sh """
                echo "Deploy to Artifactory"
                """
            }
        }
    }   
}*/
pipeline {
    agent {
        node {
            def server
            def rtMaven = Artifactory.newMavenBuild()
            def buildInfo
        }
    }

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

    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
        server = Artifactory.server jphiz

        // Tool name from Jenkins configuration
        rtMaven.tool = Maven
        rtMaven.deployer releaseRepo: default-maven-local, snapshotRepo: default-maven-local, server: server
        rtMaven.resolver releaseRepo: default-maven-virtual, snapshotRepo: default-maven-virutal, server: server
        buildInfo = Artifactory.newBuildInfo()
    }

    stage ('Exec Maven') {
        rtMaven.run goals: 'clean install', buildInfo: buildInfo
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
