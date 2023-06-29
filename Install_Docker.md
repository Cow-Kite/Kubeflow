# 1. Repository 업데이트 및 필요한 패키지 설치

    sudo apt-get update

    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg \
        lsb-release

# 2. Docker GPG key 추가

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 3. Docker repository 추가
### focal은 Ubuntu 20.04의 코드 네임, Ubuntu 버전에 따라서 코드 네임을 바꿔야 함

    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

# 4. Repository 업데이트

    sudo apt-get update

# 5. Docker 설치

    sudo apt-get install docker-ce docker-ce-cli containerd.io

# 6. Docker 설치 (버전) 확인

    sudo docker version