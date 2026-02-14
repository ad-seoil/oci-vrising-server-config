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
5. Ghost Process due to Long-term Idle State

Issue
Status: The container status was displayed as Up, but actual game connection was impossible.

Symptom: Log updates had ceased for approximately two weeks (Jan 17 – Jan 31).

Root Cause
Absence of Active Connections: The server remained in an Idle state for two weeks due to a lack of user traffic.

Resource Reclamation & Deadlock: Due to the resource policies of the Oracle Cloud (OCI) Free Tier or the memory management characteristics of the Unity engine, the process operations were effectively frozen during the prolonged low-load period.

JobTempAlloc Warnings: Log analysis revealed memory lifetime expiration warnings related to JobTempAlloc. This was caused by the system missing the memory release timing during the initial loading of massive entities (approx. 340,000). This likely contributed to the long-term process hang.

Solution
Manual Recovery: Forced a restart of the container and game process using the sudo docker restart command.

Integrity Check: During the restart process, a SteamCMD integrity check and update were automatically performed to ensure the server engine was up to date.

Verification: Confirmed service restoration by verifying the loading of 346,553 entities and the final Startup Completed log entry.

Prevention & Recommendations
Scheduled Restarts: To ensure long-term server stability and prevent memory fragmentation/freezing, it is highly recommended to implement a weekly scheduled restart.
