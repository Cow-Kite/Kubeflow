# 1. StorageClass(local-Path) 설치 및 default로 설정

    kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
    kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}
  

# 2. Kustomize 5.0.0 설치 

    wget https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv5.0.0/kustomize_v5.0.0_linux_amd64.tar.gz
    tar -xvf kustomize_v5.0.0_linux_amd64.tar.gz
    mv kustomize /usr/local/bin

# 3. Kubeflow 설치
### 컴포넌트 전부 설치 (원하는 것만 설치할 수도 있음)

    mkdir kubeflow
    cd kubeflow
    git clone https://github.com/kubeflow/manifests.git
    cd manifests

    # 명령어 한 줄로 컴포넌트 모두 설치
    while ! kustomize build example | awk '!/well-defined/' | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done

# 4. authservice-0 pod ERROR (open /var/lib/authservice/data.db: permission denied)

    kubectl edit statefulset -n istio-system authservice

    # spec: 밑에 추가 
    initContainers:
      - name: fix-permission
        image: busybox
        command: ['sh', '-c']
        args: ['chmod -R 777 /var/lib/authservice;']
        volumeMounts:
        - mountPath: /var/lib/authservice
          name: data


