pipeline {
     // 전역 에이전트를 사용하지 않음으로써 컨테이너 중첩 방지
    agent none 

    environment {
        REACT_APP_VERSION = '1.0.$BUILD_ID'
        APP_NAME = 'myjenkinsapp'
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        AWS_DOCKER_REGISTRY = '857931482269.dkr.ecr.ap-northeast-2.amazonaws.com'
        AWS_ECS_CLUSTER = 'infinite-cat-0yei20'
        AWS_ECS_SERVICE_PROD = 'LeanJenkinsApp-Service-prod'
        AWS_ECS_TD_PROD = 'LearnJenkinsApp-TaskDefinition-Prod'
    }

   stages {

        stage('Build') {
            agent {
                docker { 
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy' 
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo '빌드 시작..'
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

            // 빌드 이후에 수행
        stage('Build Docker image') {
            agent {
                docker { 
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint='' -v /var/run/docker.sock:/var/run/docker.sock"
                }
            }
            steps {
                sh '''
                    yum install -y docker
                    # 애플 실리콘칩(M1,M2 등) 사용자는 해당 옵션 붙이기
                    docker build --platform linux/amd64 -t $AWS_DOCKER_REGISTRY/$APP_NAME:$REACT_APP_VERSION .
                    aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_DOCKER_REGISTRY
                    docker push $AWS_DOCKER_REGISTRY/$APP_NAME:$REACT_APP_VERSION
                    # docker build -t myjenkinsapp .
                '''
            }
        }
    }
}
