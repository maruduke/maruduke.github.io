dockerfile 생성 -> jenkins image에 docker를 설치함

```dockerfile
FROM jenkins/jenkins:jdk17

ENV DEBIAN_FRONTEND noninteractive
ENV DEBCONF_NOWARNINGS="yes"

ARG HOST_UID=502

USER root
RUN apt-get -y update && apt-get install -y --no-install-recommends \
    vim \
    apt-utils
RUN apt-get install ca-certificates curl gnupg lsb-release -y
RUN mkdir -p /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
RUN apt-get -y update
RUN apt-get install docker-ce docker-ce-cli containerd.io docker-compose docker-compose-plugin -y
RUN if [ -e /var/run/docker.sock ]; then chown jenkins:jenkins /var/run/docker.sock; fi

RUN usermod -u $HOST_UID jenkins
RUN usermod -aG docker jenkins

USER jenkins

```

jenkins container 실행하기

```sh

# 호스트에서 접근가능하도록 설정
$ sudo chmod 666 /var/run/docker.sock

# 젠킨스 접근 설정 허용
$ chown 1000 ${호스트 볼륨 위치}

$ docker run -d   --name jenkins-dood   -p 8080:8080   -p 50000:50000   -v /var/run/docker.sock:/var/run/docker.sock   -v ${호스트 볼륨 위치}:/var/jenkins_home -u 1000  jenkins-dood
```

jenkins 파이프라인을 통하여 이미지 빌드시 설정해줘야 하는것
environment를 설정해주어야 다음과 같이 경로 오류가 발생안함

> ERROR: mkdir /.docker: permission denied

```pipeline
pipeline {
    agent any

    environment {
                HOME = "${env.WORKSPACE}"
    }

    stages {
        stage('git clone') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/Sophie-Ahn/pipelineTest.git'
            }
        }

        stage('build') {
            steps {
                sh '''chmod +x ./pipeline/gradlew
                        cd pipeline
                        ./gradlew clean build'''
            }
        }

        stage('cp dockerfile') {
            steps {
                withCredentials([file(credentialsId: 'createImage', variable: 'dockerfile')]) {
                    sh '''
                    cp -i ${dockerfile} ./pipeline
                    '''
                }
            }
        }

        stage('docker image create') {
            steps {
                sh '''
                    cd pipeline
                    docker build -t hello:latest . --no-cache
                '''
            }
        }


    }
}

```
