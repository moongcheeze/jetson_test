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

클러스터는 총 3대의 Jetson Orin Nano 보드로 구성됩니다.
- **Master Node**: 1대
- **Worker Node**: 2대



## 📦 STEP 2. 로컬 레지스트리 생성

## 🗂️ STEP 3. NFS 설정

## 🚀 STEP 4. 실행 예제
