pipeline {
    agent {
        kubernetes {
            label 'flyway'
            defaultContainer 'jnlp'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
                - name: flyway
                  image: flyway/flyway:latest
                  command: ['cat']
                  tty: true
            """
        }
    }
    environment {
        SNOWFLAKE_ACCOUNT = credentials('snowflake_account')
        SNOWFLAKE_USER = credentials('snowflake_user')
        SNOWFLAKE_PASSWORD = credentials('snowflake_password')
        SNOWFLAKE_DATABASE = 'your_database'
        SNOWFLAKE_SCHEMA = 'your_schema'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Run Migrations') {
            steps {
                container('flyway') {
                    script {
                        // Configure Flyway to connect to Snowflake
                        sh """
                        flyway -url=jdbc:snowflake://${SNOWFLAKE_ACCOUNT}/${SNOWFLAKE_DATABASE}/${SNOWFLAKE_SCHEMA}
                        -user=${SNOWFLAKE_USER} -password=${SNOWFLAKE_PASSWORD} migrate
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                // Clean up or notify
                echo 'Pipeline finished.'
            }
        }
        success {
            echo 'Migrations completed successfully!'
        }
        failure {
            echo 'Migrations failed.'
        }
    }
}
