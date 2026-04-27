pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
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
                    def javaDir = tool name: 'JDK17', type: 'jdk'
                    def mvnDir = tool name: 'Maven3', type: 'maven'

                    sh """
                        # Dynamically resolve absolute paths to bypass Jenkins UI extraction misconfigurations
                        ACTUAL_JAVA_HOME=\$(find "${javaDir}" -type f -name "java" | grep "bin/java" | head -n 1 | sed 's|/bin/java||')
                        ACTUAL_MVN_HOME=\$(find "${mvnDir}" -type f -name "mvn" | grep "bin/mvn" | head -n 1 | sed 's|/bin/mvn||')

                        export JAVA_HOME=\$ACTUAL_JAVA_HOME
                        export M2_HOME=\$ACTUAL_MVN_HOME
                        export PATH=\$M2_HOME/bin:\$JAVA_HOME/bin:\$PATH

                        mvn clean compile
                    """
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def javaDir = tool name: 'JDK17', type: 'jdk'
                    def mvnDir = tool name: 'Maven3', type: 'maven'

                    sh """
                        ACTUAL_JAVA_HOME=\$(find "${javaDir}" -type f -name "java" | grep "bin/java" | head -n 1 | sed 's|/bin/java||')
                        ACTUAL_MVN_HOME=\$(find "${mvnDir}" -type f -name "mvn" | grep "bin/mvn" | head -n 1 | sed 's|/bin/mvn||')

                        export JAVA_HOME=\$ACTUAL_JAVA_HOME
                        export M2_HOME=\$ACTUAL_MVN_HOME
                        export PATH=\$M2_HOME/bin:\$JAVA_HOME/bin:\$PATH

                        mvn test
                    """
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/junitreports/*.xml'
                    jiraSendBuildInfo
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