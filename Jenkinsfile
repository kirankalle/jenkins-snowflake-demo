pipeline {
    agent any
    environment {
        // Environment variables for Snowflake connection
        SNOWFLAKE_URL = credentials('snowflake-url')  // Snowflake account URL
        SNOWFLAKE_USER = credentials('snowflake-username') // Username
        SNOWFLAKE_PASSWORD = credentials('snowflake-password') // Password
        FLYWAY_VERSION = '10.17.3' // Specify the Flyway version
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your repository
                git branch: 'main', url: 'https://github.com/kirankalle/jenkins-snowflake-demo.git'
            }
        }

        stage('Install Flyway') {
            steps {
                script {
                    // Download and install Flyway
                    sh "rm -rf flyway"
                    sh "wget -qO- https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/${FLYWAY_VERSION}/flyway-commandline-${FLYWAY_VERSION}-linux-x64.tar.gz | tar xvz"
                    sh "mv flyway-${FLYWAY_VERSION} flyway"
                }
            }
        }

        stage('Run Flyway Migrations') {
            steps {
                script {
                    // Use withCredentials to handle secrets securely
                    withCredentials([usernamePassword(credentialsId: 'snowflake-credentials', usernameVariable: 'SNOWFLAKE_USER', passwordVariable: 'SNOWFLAKE_PASSWORD')]) {
                        // Construct the Flyway command
                        def flywayCommand = """
                        ./flyway/flyway \
                        -url='jdbc:snowflake://gvb61328.us-east-1.snowflakecomputing.com/?db=JAR_DB' \
                        -user='${SNOWFLAKE_USER}' \
                        -password='${SNOWFLAKE_PASSWORD}' \
                        -locations=filesystem:sql \
                        migrate
                        """

                        // Run the Flyway migrations
                        sh "${flywayCommand}"
                    }
                }
            }
        }

        stage('Test Migrations') {
            steps {
                script {
                    // Run any tests or validations needed after migration
                    sh "./flyway/flyway -url=${SNOWFLAKE_URL} -user=${SNOWFLAKE_USER} -password=${SNOWFLAKE_PASSWORD} info"
                }
            }
        }
    }

    post {
        success {
            echo 'Migrations completed successfully!'
        }
        failure {
            echo 'Migrations failed!'
        }
    }
}
