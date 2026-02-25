pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh '''
                    set -e

                    echo "Building Java project..."
                    ls

                    cd "Password Protection"

                    mkdir -p build

                    javac -d build src/*.java

                    echo "Build successful"
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    set -e

                    echo "Running JUnit tests..."

                    cd "Password Protection"

                    # Remove old jar safely (only inside this folder)
                    rm -f junit-platform-console-standalone.jar

                    echo "Downloading JUnit..."
                    curl -L -f -o junit-platform-console-standalone.jar \
                    https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar

                    # Verify download
                    if [ ! -s junit-platform-console-standalone.jar ]; then
                        echo "JUnit download failed!"
                        exit 1
                    fi

                    mkdir -p test-build

                    javac -cp junit-platform-console-standalone.jar:build \
                          -d test-build test/*.java

                    java -jar junit-platform-console-standalone.jar \
                         --class-path build:test-build \
                         --scan-class-path

                    echo "Tests completed successfully"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -e

                    echo "Packaging application..."

                    cd "Password Protection"

                    jar cf FileEncrypter.jar -C build .

                    echo "Deployment successful"
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}