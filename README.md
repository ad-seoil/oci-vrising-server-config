# V Rising Server Infrastructure on OCI

Oracle Cloud(OCI) 環境で Dockerを利用して構築したV Rising専用サーバー設定理REPOSITORYです。

## 🛠 Tech Stack
- **Environment:** Oracle Cloud Infrastructure (Ubuntu)
- **Containerization:** Docker, Docker Compose
- **Server Image:** `tsxcloud/vrising-ntsync`

## 🚀 How to Run
```bash
# Clone the repository
git clone [https://github.com/ad-seoil/oci-vrising-server-config.git](https://github.com/ad-seoil/oci-vrising-server-config.git)

# Run the server
docker-compose up -d
```

## 🔍 Troubleshooting
1. Assertion Failed: GetBestRoute2 failed with result 50
```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
```
2. Assertion Failed: GetBestRoute2 failed with result 50
```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1
```
3. Assertion Failed: GetBestRoute2 failed with result 50
```
sudo vi /etc/sysctl.conf
-----------------------
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
-----------------------
sudo sysctl -p
```
```
sudo vi /home/ubuntu/vrising/data/Settings/ServerHostSettings.json
"Secure": true, -> "Secure": false,
```
->
IPv6 강제 닫기
Secure: False: 스팀 인증 서버와의 통신 과정(핸드셰이크)에서 발생하는 네트워크 병목과 경로 오류를 제거했습니다. 덕분에 클라이언트의 패킷이 중간 단계 없이 서버로 직통(Direct)

4. Assertion Failed: GetBestRoute2 failed with result 50
```
sudo vi /home/ubuntu/vrising/data/Settings/ServerHostSettings.json
"LanMode": false, -> "LanMode": true,
```
5. 장기 유휴 상태로 인한 프로세스 고스트(Ghost Process) 현상

Issue: 컨테이너 상태는 Up으로 표시되나, 실제 게임 접속이 불가능하고 로그 갱신이 약 2주간(1/17~1/31) 중단됨.

Root Cause: - Active Connection 부재: 2주간 접속자(User)가 없어 서버가 유휴(Idle) 상태로 지속됨.

자원 회수 및 데드락: OCI(Oracle Cloud) 무료 티어의 정책 혹은 Unity 엔진의 메모리 관리 특성상, 장시간 저부하 상태에서 실질적인 프로세스 연산이 동결(Freeze)됨.

JobTempAlloc 경고: 로그 확인 결과 JobTempAlloc 관련 메모리 수명 초과 경고 발생. 이는 초기 대량의 엔티티(약 34만 개) 로딩 시 메모리 반납 타이밍을 놓쳐 발생한 것으로, 장기적으로 프로세스 행(Hang)의 원인이 될 수 있음을 파악함.

Solution
``` sudo docker restart``` 명령을 통해 컨테이너 및 게임 프로세스를 강제 재시작.

재시작 과정에서 SteamCMD 무결성 검사 및 업데이트를 자동 수행하여 서버 엔진을 최신화함.

346,553개의 엔티티 로딩 및 Startup Completed 로그를 최종 확인하여 서비스 정상화 완료.

Prevention: 서버 안정성을 위해 주 1회 정기적인 프로세스 재시작(Scheduled Restart) 권장.
