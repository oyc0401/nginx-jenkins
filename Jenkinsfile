pipeline {
    agent any

    environment {
        EC2_HOST  = 'ubuntu@3.37.136.153'     // 배포 대상
        REPO_DIR  = '/home/ubuntu/nginx-docker' // EC2에 둘 디렉터리
        GIT_REPO  = 'https://github.com/oyc0401/nginx-jenkins' // 이 저장소 URL
    }

    stages {

        stage('Deploy Nginx Container') {
            steps {
                // EC2에 SSH 접속할 자격 (private key) 는 'ec2-ssh-key'로 저장돼 있다고 가정
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_HOST} '
                        set -e

                        # 1) 저장소 최초 clone 또는 pull
                        if [ ! -d ${REPO_DIR} ]; then
                            git clone ${GIT_REPO} ${REPO_DIR}
                        else
                            cd ${REPO_DIR} && git pull --ff-only
                        fi

                        # 2) 컨테이너 재시작 (무중단 필요시 docker compose up -d --pull=always 로 대체)
                        cd ${REPO_DIR}
                        docker compose down || true
                        docker compose pull nginx
                        docker compose up -d

                        # 3) 상태 확인
                        docker ps --filter "name=nginx"
                    '
                    """
                }
            }
        }
    }
}
