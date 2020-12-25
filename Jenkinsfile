pipeline {
    agent {
        node {
            label 'metersphere'
        }
    }
    options { quietPeriod(600) }
    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'metersphere', description: '构建后的 Docker 镜像名称')
        string(name: 'IMAGE_PREFIX', defaultValue: '10.10.4.190:5000/metersphere', description: '构建后的 Docker 镜像带仓库名的前缀')
        string(name: 'TAG_VERSION', defaultValue: 'latest', description: '镜像版本号')
    }
    stages {
        stage('Build/Test') {
            steps {
                sh "env"
                sh "/opt/webapps/tools/hudson.tasks.Maven_MavenInstallation/maven/bin/mvn clean package -Dmaven.test.skip=true"
            }
        }
        stage('Docker build & push') {
            steps {
                sh "docker build --build-arg MS_VERSION=\${TAG_NAME:-\$BRANCH_NAME}-\${GIT_COMMIT:0:8} -t ${IMAGE_NAME}:${TAG_VERSION} ."
                sh "docker tag ${IMAGE_NAME}:${TAG_VERSION} ${IMAGE_PREFIX}/${IMAGE_NAME}:${TAG_VERSION}"
                sh "docker push ${IMAGE_PREFIX}/${IMAGE_NAME}:${TAG_VERSION}"
            }
        }
        stage('Docker run') {
            steps {
                sh "docker rm -f metersphere |true"
                sh "docker run -d --name metersphere -v /opt/metersphere:/opt/metersphere -p 8081:8081 ${IMAGE_NAME}:${TAG_VERSION}"
            }
        }
    }
}