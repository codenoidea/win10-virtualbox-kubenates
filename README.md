# win10-virtualbox-kubernetes
윈도우10에 virtualbox에 kubernetes 설치하기

# virtualbox 머신 생성
```
종류: linux 
버전: ubuntu 64bit
메모리크기: 2GB이상
하드디스크 파일종류: vdi선택
파일 위치 및 크기: 20gb
```

# virtualbox 머신 설정
```
시스템 > 프로세서: cpu 2개 이상
저장소 > 컨트롤러: ubuntu-18.04.3-desktop-amd64.iso
네트워크 > 어댑터1에서 이름을 어댑터 브리지 선택/ Host 네트워크 설정을 위해 어댑터 2 > 네트워크 어댑터 사용하기 체크 > 호스트 전용 어댑터를 선택 
```

# master 머신 시작
```
우분투 설치 생략...
클립보드 공유를 위해 장치 > 클립보드 공유 > 양방향 선택
```
터미널 접속
```
root 계정으로 작업하기 위해 sudo -i 실행
apt update 실행
apt install openssh-server 실행
apt install net-tools 실행
방화벽해제: ufw disable 실행
방화벽확인: ufw status verbose 실행
apt-get install vim 실행
```
네트워크 설정(터미널)
```
vi /etc/hosts 실행
 (아래내용 저장)
 192.168.72.101 master
 192.168.72.102 node01
 192.168.72.103 node02

hostname 변경: hostnamectl set-hostname master
```
Host Only Ethernet 설정
```
cd /etc/netplan/ 실행
vi 01-network-manager-all.yaml 실행
 (아래내용 저장)
 # Let NetworkManager manage all devices on this system
 network:
   version: 2
   renderer: NetworkManager
   ethernets:
     enp0s8:
       dhcp6: no
       addresses:
       - 192.168.72.101/24
       gateway4: 192.168.72.1
       nameservers:
         addresses: [8.8.8.8, 8.8.4.4]
설정 저장: netplan apply 실행
호스트확인: hostname -I 실행
reboot 실행
```
도커 및 쿠버네티스 설치
```
루트계정: sudo -i 실행
apt install docker.io 실행
스왑중단: swapoff -a 실행
스왑완전중단: sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab 실행
쉘 생성: vi kubeadm-install.sh
 (아래내용 저장)
 sudo apt-get update && sudo apt-get install -y apt-transport-https curl
 curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
 cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
 deb https://apt.kubernetes.io/ kubernetes-xenial main
 EOF
 sudo apt-get update
 sudo apt-get install -y kubelet kubeadm kubectl
 sudo apt-mark hold kubelet kubeadm kubectl
 
sh kubeadm-install.sh 실행
설치확인: kubeadm version 실행
마스터 머신 중지
```
# 워커머신 생성
```
마스터 머신 복제
MAC 주소 정책에서 모든 네트워크 어댑터의 새 MAC 주소 생성 선택
완전한 복제를 선택
```
워커머신 시작
```
클립보드 공유를 위해 장치 > 클립보드 공유 > 양방향 선택
```
워커머신 터미널 
```
sudo -i 실행
호스트네임 변경: hostnamectl set-hostname node01 실행
```
Host Only Ethernet 설정
```
cd /etc/netplan/ 실행
vi 01-network-manager-all.yaml 실행
 (아래내용 저장)
 # Let NetworkManager manage all devices on this system
 network:
   version: 2
   renderer: NetworkManager
   ethernets:
     enp0s8:
       dhcp6: no
       addresses:
       - 192.168.72.102/24
       gateway4: 192.168.72.1
       nameservers:
         addresses: [8.8.8.8, 8.8.4.4]
설정 저장: netplan apply 실행
호스트확인: hostname -I 실행
reboot 실행
```
마스터 머신 시작 후 터미널 접속
```
sudo -i 실행
kubeadm init 실행
 mkdir부터 3줄까지 복사 후 터미널 새탭열고 붙여넣기 실행
Kubeadm join~ 복사
```
워커머신 터미널 접속
```
Kubeadm join~ 복사한것 실행
```
마스터머신 터미널 접속
```
kubectl get nodes 실행
```
# Overlay Network 설치
마스터머신 터미널 접속
```
Weave net을 설치: kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')" 실행
kubectl get nodes 실행
kubectl create deploy nginx-deploy --image=nginx 실행
kubectl get pods -o wide 실행
kubectl delete deploy nginx-deploy 실행
```

# 참고
https://cla9.tistory.com/90?category=814452
