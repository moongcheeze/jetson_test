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

클러스터는 총 3대의 Jetson Orin Nano 보드로 구성했으며,각 보드는 스위치를 통해 동일한 네트워크에 연결됩니다.

- 스위치와 라우터를 LAN 케이블로 연결
- 스위치와 3대의 Jetson Orin Nano 보드를 LAN 케이블로 연결
- 스위치 전원 케이블 연결
- 각 Jetson Orin Nano 보드 전원 케이블 연결

<br>

### 1-2. 기본 설정 세팅

<br>

### 1-3. 고정 IP 주소 설정

<br>

### 1-4. 쿠버네티스 설치




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
