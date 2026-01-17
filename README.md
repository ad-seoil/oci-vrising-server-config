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
First Assertion Failed: GetBestRoute2 failed with result 50
```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
```
Second Assertion Failed: GetBestRoute2 failed with result 50
```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1
```
Third Assertion Failed: GetBestRoute2 failed with result 50
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

Forth Assertion Failed: GetBestRoute2 failed with result 50
```
sudo vi /home/ubuntu/vrising/data/Settings/ServerHostSettings.json
"LanMode": false, -> "LanMode": true,
```
