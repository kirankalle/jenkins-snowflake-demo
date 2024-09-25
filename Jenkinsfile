pipeline {
    agent any

    environment {
        SNOWFLAKE_ACCOUNT = credentials('snowflake-account')  // Snowflake account credentials
        SNOWFLAKE_USER = credentials('snowflake-username')
        SNOWFLAKE_PASSWORD = credentials('snowflake-password')
        FLYWAY_VERSION = '8.5.0' // Specify the Flyway version
        GIT_REPO_URL = 'https://github.com/your/repo.git'  // Replace with your repo URL
        GIT_BRANCH = 'main'  // Specify the branch to use
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout code from the specified Git repository
                git branch: GIT_BRANCH, url: GIT_REPO_URL
            }
        }

        stage('Install Flyway') {
            steps {
                script {
                    // Download and install Flyway
                    sh """
                    wget https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/${FLYWAY_VERSION}/flyway-commandline-${FLYWAY_VERSION}-linux-x64.zip
                    unzip flyway-commandline-${FLYWAY_VERSION}-linux-x64.zip
                    mv flyway-${FLYWAY_VERSION} flyway
                    """
                }
            }
        }

        stage('Run Flyway Migrations') {
            steps {
                script {
                    // Run Flyway migrations
                    sh """
                    ./flyway/flyway -url=jdbc:snowflake://${SNOWFLAKE_ACCOUNT}.snowflakecomputing.com \
                                   -user=${SNOWFLAKE_USER} \
                                   -password=${SNOWFLAKE_PASSWORD} \
                                   -locations=filesystem:./sql migrate
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'DDL deployment successful!'
        }
        failure {
            echo 'DDL deployment failed.'
        }
    }
}
