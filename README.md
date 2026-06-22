<div align="center">

<h1>쿠버네티스 기반 Jetson 클러스터 구축 및 실행 가이드</h1>

<img
  width="600"
  alt="Jetson cluster architecture"
  src="https://github.com/user-attachments/assets/4baa5a25-5095-40fa-a78d-d02b2e6a6fef"
  style="margin: 24px 0;"
/>

<p>
본 레포지토리는 Jetson Orin Nano 보드를 이용해 쿠버네티스 클러스터를 구성하는 과정을 설명합니다.<br>
이후 노드 간 컨테이너 이미지 공유를 위한 로컬 레지스트리 구축과 실행 결과 저장을 위한 NFS 설정 과정을 정리합니다.<br>
마지막으로 구성한 클러스터 위에서 실행가능한 예제(SCALE-Sim v3 이용)를 제공합니다.
</p>
</div>
<br>
<br>


## 🛠 STEP 1. 클러스터 구축

### (1) 보드 및 스위치 세팅

<p align="center">
  <img
    width="600"
    alt="Jetson cluster"
    src="https://github.com/user-attachments/assets/70bc655e-c149-43dc-99c4-261053dc6058"
  />
</p>

Jetson Orin Nano 보드 3대와 스위치를 이용하여 클러스터를 구성했습니다.
> 스위치와 라우터를 LAN 케이블로 연결  
> 스위치와 3대의 Jetson Orin Nano 보드를 LAN 케이블로 연결  
> 스위치 전원 케이블 연결  
> 각 Jetson Orin Nano 보드 전원 케이블 연결 

<br>


### (2) 내부 IP 및 SSH 접속 설정

- SSH 접속을 위해 공유기 관리 페이지에서 각 Jetson Orin Nano 보드의 내부 IP와 포트를 설정합니다.
  
  ```text
  공유기 IP: 192.168.176.47
  
  Master Node  : 192.168.0.24  / SSH Port 2222
  Worker Node 1: 192.168.0.25  / SSH Port 2223
  Worker Node 2: 192.168.0.26  / SSH Port 2224
  ```
  
- 학교 네트워크 서버에 접속한 뒤, 다음 명령어로 각 노드에 SSH 접속할 수 있습니다.

  ```bash
  ssh <username>@192.168.176.47 -p <port>
  ```

- 예를 들어, Master Node에 접속하려면 다음 명령어를 실행합니다.

  ```bash
  ssh <username>@192.168.176.47 -p 2222
  ```

<br>

### (3) 기본 환경 설정


- 기본 부팅 모드를 text mode로 변경하여, 부팅 시 GUI 환경이 아닌 CLI 환경으로 실행되도록 설정합니다.  
(추후 GUI 환경이 필요한 경우, 기본 부팅 모드를 다시 `graphical.target`으로 변경할 수 있습니다.)
  
  ```bash
  sudo systemctl set-default multi-user.target
  ```

- 다음 명령어를 통해 Jetson 보드를 최대 성능 모드로 설정합니다.

  ```bash
  sudo nvpmodel -m 0
  ```

- 쿠버네티스는 예측 가능한 자원 관리를 위해 swap memory가 비활성화된 환경을 권장합니다.  
  swap이 활성화되어 있으면 컨테이너 자원 관리 과정에서 예상하지 못한 동작이 발생할 수 있습니다.  
  다음 명령어를 통해 swap memory를 비활성화합니다.

  ```bash
  sudo swapoff -a
  ```

- Docker 컨테이너에서 GPU를 기본적으로 사용할 수 있도록 NVIDIA runtime을 기본 Docker runtime으로 설정합니다.  
  Docker 설정 파일에 `"default-runtime": "nvidia"` 코드를 추가해줍니다.

  ```bash
  sudo vi /etc/docker/daemon.json
  ```

  `daemon.json` 파일을 다음과 같이 수정합니다.
  
  ```json
  {
    "default-runtime": "nvidia",
    "runtimes": {
      "nvidia": {
        "args": [],
        "path": "nvidia-container-runtime"
      }
    }
  }
  ```

  설정을 적용하기 위해 Docker를 재시작합니다.

  ```bash
  sudo systemctl restart docker
  ```

  다음 명령어를 통해 NVIDIA runtime이 기본 runtime으로 설정되었는지 확인합니다.

  ```bash
  sudo docker info | grep -i "Default Runtime"
  ```

  출력 결과에 `nvidia`가 표시되면 설정이 정상적으로 적용된 것입니다.

  ```text
  Default Runtime: nvidia
  ```

- Docker 명령어를 실행할 때마다 `sudo`를 사용하지 않도록, 현재 사용자를 `docker` group에 추가합니다.

  ```bash
  # Create the docker group
  sudo groupadd docker

  # Add the current user to the docker group
  sudo usermod -aG docker $USER

  # Start a new session with the updated group
  newgrp docker
  ```

- 패키지 목록을 최신 상태로 업데이트하고, 설치된 패키지를 최신 버전으로 업그레이드합니다.

  ```bash
  sudo apt-get update
  sudo apt-get dist-upgrade
  ```

- 시스템 업데이트 이후에는 재부팅을 권장합니다.

  ```bash
  sudo reboot
  ```


<br>

### (4) 고정 IP 주소 설정
- **(Optional)** 쿠버네티스 클러스터 운영 시 재부팅 후에도 동일한 주소로 각 노드에 접근할 수 있도록, Jetson 보드에 고정 IP 주소를 할당해주는 과정입니다.
- 고정 IP 주소를 설정하기 위해 Netplan을 설치합니다. Netplan은 YAML 파일을 사용해 Ubuntu의 네트워크 설정을 정의하는 도구입니다.

  ```bash
  sudo apt install netplan.io
  ```

- 고정 IP 주소를 설정하기 전에 현재 네트워크 정보를 확인합니다.  
  해당 정보는 이후 Netplan YAML 설정 파일을 작성할 때 필요합니다.

  ```bash
  # Check the current IP address
  ifconfig

  # Check the default gateway
  route -n

  # Check the DNS nameserver
  cat /etc/resolv.conf
  ```  

- Netplan 설정 파일을 수정하여 DHCP를 비활성화하고 고정 IP 주소를 할당합니다.  
  IP 주소, gateway, DNS nameserver 정보는 사용자의 네트워크 환경에 맞게 변경합니다.

  ```bash
  sudo vi /etc/netplan/config.yaml
  ```

  ```yaml
  network:
    version: 2
    renderer: networkd
    ethernets:
      eno1:
        dhcp4: no
        addresses:
          - 192.168.0.24/24   # Static IP address with subnet prefix
        routes:
          - to: default
            via: 192.168.0.1   # Default gateway address
        nameservers:
          addresses:
            - 127.0.0.53   # Local DNS resolver
            - 8.8.8.8
  ```

- Netplan 설정 파일은 `root`가 소유해야 하며, 제한된 권한을 가져야 합니다.

  ```bash
  sudo chmod 600 /etc/netplan/config.yaml
  sudo chown root:root /etc/netplan/config.yaml
  ```

- 권한 수정 후 Netplan 설정을 적용합니다.

  ```bash
  sudo netplan apply
  ```

- Jetson Orin Nano 보드에서는 다음과 같은 `ovsdb-server.service` 경고가 출력될 수 있습니다.  
  해당 경고는 실험 환경에 영향을 주지 않으므로 무시해도 됩니다.

  ```text
  WARNING:root:Cannot call Open vSwitch: ovsdb-server.service is not running.
  ```

- 다음 명령어를 사용하여 고정 IP 설정이 정상적으로 적용되었는지 확인합니다.

  ```bash
  netplan status eno1
  ```
  
- `Routes` 항목에서 default gateway가 `static`으로 표시되면 성공입니다.

  ```text
  default via 192.168.0.1 (static)
  192.168.0.0/24 from 192.168.0.24 (link)
  ```


<br>

### (5) 쿠버네티스 설치

- k3s는 Jetson 보드와 같이 자원이 제한된 환경에서 사용하기 적합한 경량 쿠버네티스 배포판입니다.  
  하나의 Jetson 보드는 마스터 노드(k3s server)로, 나머지 보드들은 워커 노드(k3s agent)로 설정합니다.
  
<p align="center">
  <img
    width="600"
    alt="k3s cluster overview"
    src="https://github.com/user-attachments/assets/d5d7b970-f5bc-441f-bf54-2bf13f3a439a"
  />
</p>

- 마스터 노드로 사용할 보드의 IP 주소를 확인한 뒤, 아래 k3s 설치 명령어를 실행합니다.  
  `--advertise-address`도 마스터 노드의 IP 주소를 그대로 입력합니다.

  ```bash
  curl -sfL https://get.k3s.io | sh -s - \
    --docker \
    --node-name master \
    --node-ip 192.168.0.24 \
    --advertise-address 192.168.0.24
  ```

  설치가 완료되면 마스터 노드가 클러스터에 등록되었는지 확인합니다.
  
  ```bash
  sudo k3s kubectl get nodes
  ```
  
  실행 결과 예시는 다음과 같습니다.
  
  ```text
  NAME     STATUS   ROLES           AGE    VERSION
  master   Ready    control-plane    179m   v1.34.3+k3s1
  ```




<br>
<br>

## 📦 STEP 2. 로컬 레지스트리 생성

<br>
<br>

## 🗂️ STEP 3. NFS 설정

<br>
<br>

## 🚀 STEP 4. 실행 예제

<br>
<br>
