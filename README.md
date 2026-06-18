<div align="center">

<h1>쿠버네티스 기반 Jetson 클러스터 구축 및 실행 가이드</h1>

<img width="600" alt="Jetson cluster architecture" src="https://github.com/user-attachments/assets/4baa5a25-5095-40fa-a78d-d02b2e6a6fef" />

<br><br>

<p>
본 레포지토리는 Jetson Orin Nano 보드를 이용해 쿠버네티스 클러스터를 구성하고,<br>
Local Registry와 NFS를 설정한 뒤 예제 Pod를 실행하는 과정을 정리한 가이드입니다.
</p>

<p>
클러스터는 총 3대의 Jetson Orin Nano 보드로 구성됩니다.
</p>

<table>
  <tr>
    <td><b>Master Node</b></td>
    <td>1대</td>
  </tr>
  <tr>
    <td><b>Worker Node</b></td>
    <td>2대</td>
  </tr>
</table>

</div>
