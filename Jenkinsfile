pipeline {
    agent any
    environment {
        // Environment variables for Snowflake connection
        SNOWFLAKE_URL = credentials('snowflake-url')  // Snowflake account URL
        SNOWFLAKE_USER = credentials('snowflake-username') // Username
        SNOWFLAKE_PASSWORD = credentials('snowflake-password') // Password
        FLYWAY_URL = "jdbc:snowflake://gvb61328.us-east-1.snowflakecomputing.com/?db=JAR_DB&warehouse=CICD_DEMO&role=ACCOUNTADMIN"
        FLYWAY_LOCATION = "filesystem:sql migrate"
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
                    sh "wget -qO- https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/10.17.3/flyway-commandline-10.17.3-linux-x64.tar.gz | tar xvz"
                    sh "sudo ln -s `pwd`/flyway-10.17.3/flyway /usr/local/bin/flyway"
                }
            }
        }

        stage('Run Migrations') {
            steps {
                script {
                    // Set Flyway configuration 
                    sh """
                    flyway -url=$FLYWAY_URL \
                    -user=$SNOWFLAKE_USER \
                    -password=$SNOWFLAKE_PASSWORD \
                    -locations=$FLYWAY_LOCATION \
                    migrate
                    """
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
