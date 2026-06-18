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
이후 노드 간 컨테이너 이미지 공유를 위한 로컬 레지스트리 구축과 실행 결과 저장을 위한 NFS(Network File System) 설정 과정을 정리합니다.<br>
마지막으로 구성한 클러스터 위에서 예제(SCALE-Sim v3 이용)를 실행하는 방법을 설명합니다.
</p>
</div>
<br>
<br>


## 🛠 STEP 1. 클러스터 구축

### 1-1. 보드 및 스위치 세팅

<p align="center">
  <img
    width="600"
    alt="Jetson cluster"
    src="https://github.com/user-attachments/assets/70bc655e-c149-43dc-99c4-261053dc6058"
  />
</p>

클러스터는 총 3대의 Jetson Orin Nano 보드로 구성했고, 각 보드는 스위치를 통해 동일한 네트워크에 연결됩니다.
> 스위치와 라우터를 LAN 케이블로 연결  
> 스위치와 3대의 Jetson Orin Nano 보드를 LAN 케이블로 연결  
> 스위치 전원 케이블 연결  
> 각 Jetson Orin Nano 보드 전원 케이블 연결 

<br>


### 1-2. 내부 IP 및 SSH 접속 설정

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

### 1-3. 기본 환경 설정


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


<br>

### 1-4. 고정 IP 주소 설정

<br>

### 1-5. 쿠버네티스 설치




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
