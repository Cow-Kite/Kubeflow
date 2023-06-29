# 1. 모든 노드 (마스터 및 워커) 에서 수행
### 1-1. 각 노드에서 권한 변경

    sudo su

### 1-2. 모든 노드에서 swap, 방화벽 비활성화

    swapoff -a
    sed -i '/swap/d' /etc/fstab
    sudo ufw disable

### 1-3. 쿠버네티스 설치에 필요한 패키지 추가

    apt-get update && apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update

### 1-4. 쿠버네티스 패키지 설치 (2023.6 기준 kubeflow 사용시 1.25 버전으로 설치)

    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

# 2. 마스터 노드에서 초기화 수행 (calico 설치 예정, kubeadm 메시지 저장 -> 워커 노드 조인시 사용)

    sudo kubeadm init --pod-network-cidr=192.168.0.0/16

## !! Error: container runtime is not running
### 명령어 차례대로 실행

    apt remove containerd
    apt update
    apt install containerd.io
    rm /etc/containerd/config.toml
    systemctl restart containerd

# 3. 초기화 후, 마스터노드에서 다음 명령어를 실행하여 kubectl 구성 로드

    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    export KUBECONFIG=$HOME/.kube/config

# 4. 워커 노드 조인, 각 워커 노드에서 수행

    kubeadm join <마스터 노드 IP>:6443 --token <Token 값> --discovery-token-ca-cert-hash <Cert 해시값>

# 5. 마스터 노드에서 노드 검증 (STATUS: NotReady)

    kubectl get nodes

# 6. Calico 설치

    curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/calico.yaml -O

    kubectl apply -f calico.yaml

# 7. 최종 확인 (STATUS: Ready)

    kubectl get nodes
    kubectl get pods -A
