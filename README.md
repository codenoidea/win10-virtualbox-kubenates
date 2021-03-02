# win10-virtualbox-kubenates
윈도우10에 virtualbox에 kubenates 설치하기

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
