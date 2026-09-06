pipeline {

```
agent any

environment {
    IMAGE = 'akhilag28/containerized-java'
}

stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
                url: 'https://github.com/bhuvan-raj/Jenkins-JavaDockerized.git'
        }
    }

    stage('Build JAR') {
        steps {
            sh 'mvn clean package -DskipTests'
        }
    }

    stage('Generate Dockerfile') {
        steps {
            writeFile file: 'Dockerfile', text: '''
```

FROM eclipse-temurin:17-jdk-alpine

COPY target/*.jar app.jar

EXPOSE 8081

ENTRYPOINT ["java", "-jar", "/app.jar"]
'''
}
}

```
    stage('Build Docker Image') {
        steps {
            sh '''
                docker build -t $IMAGE:latest .
                docker images | grep containerized-java
            '''
        }
    }

    stage('Push to DockerHub') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'docker-id',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )
            ]) {
                sh '''
                    echo "Docker Hub username used by Jenkins: $DOCKER_USERNAME"

                    echo "$DOCKER_PASSWORD" | docker login \
                        --username "$DOCKER_USERNAME" \
                        --password-stdin

                    echo "Pushing image: $IMAGE:latest"

                    docker push $IMAGE:latest
                '''
            }
        }
    }

    stage('Run Container') {
        steps {
            sh '''
                docker rm -f javaapp || true

                docker run -d \
                    --name javaapp \
                    -p 8081:8081 \
                    $IMAGE:latest

                docker ps
            '''
        }
    }
}

post {
    always {
        sh 'docker logout || true'
    }

    success {
        echo 'Pipeline completed successfully!'
        echo 'Application should be available on port 8081.'
    }

    failure {
        echo 'Pipeline failed. Check the failed stage above.'
    }
}
```

}
