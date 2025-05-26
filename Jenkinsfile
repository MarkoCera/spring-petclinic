pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/MarkoCera/spring-petclinic'
        REMOTE_USER = 'deployuser'
        REMOTE_HOST = '192.168.1.11'
        REMOTE_DIR = '/tmp/deploy/pipeline'
        JAR_DIR = '/opt/project2/bin'
        JAR_NAME = 'project2.jar'
    }
    
    tools {
        maven "MAVEN3.9.9"
    }

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive JAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }

        stage('Copy JAR to Remote Host') {
            steps {
                    sh """
                    scp target/*.jar $REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR/$JAR_NAME
                    """
            }
        }

        stage('Execute Remote Commands') {
            steps {
                    sh """
                    ssh $REMOTE_USER@$REMOTE_HOST '
                        mv $JAR_DIR/$JAR_NAME $JAR_DIR/${JAR_NAME}.bak
                        mv $REMOTE_DIR/$JAR_NAME $JAR_DIR
                        sudo systemctl restart project2
                    '
                    """
            }
        }
    }
}
