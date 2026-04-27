pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    environment {
        JAVA_HOME = tool('JDK17')
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                script {
                    def javaHome = tool name: 'JDK17', type: 'jdk'
                    def mvnHome = tool name: 'Maven3', type: 'maven'
                    withEnv(["JAVA_HOME=${javaHome}", "PATH=${mvnHome}/bin:${javaHome}/bin:${env.PATH}"]) {
                        sh 'mvn clean compile'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def javaHome = tool name: 'JDK17', type: 'jdk'
                    def mvnHome = tool name: 'Maven3', type: 'maven'
                    withEnv(["JAVA_HOME=${javaHome}", "PATH=${mvnHome}/bin:${javaHome}/bin:${env.PATH}"]) {
                        sh 'mvn test'
                    }
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/junitreports/*.xml'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the execution logs.'
        }
    }
}