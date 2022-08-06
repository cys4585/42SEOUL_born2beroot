# UFW (방화벽)

# 1. Firewall(방화벽)

방화벽은 미리 정의된 보안 규칙에 기반한, 들어오고 나가는 네트워크 트래픽을 모니터링하고 제어하는 네트워크 보안 시스템이다. 방화벽은 일반적으로 신뢰할 수 있는 내부 네트워크, 신뢰할 수 없는 외부 네트워크 간의 장벽을 구성한다. 서로 다른 네트워크를 지나는 데이터를 허용하거나 거부하거나 검열 및 수정하는 하드웨어나 소프트웨어 장치이다.

---

# 2. UFW(Uncompliated Fire Wall)

UFW는 데비안 계열 리눅스 환경에서 작동되는 사용하기 쉬운 방화벽 관리 프로그램이다.

UFW는 iptables를 좀 더 쉽게 설정할 수 있는 프로그램이다. 간단한 방화벽 구성은 할 수 있지만, 수준 높은 방화벽 구성을 위해서는 iptables 룰을 직접 사용해야 한다.

- iptables
    
    > iptables는 리눅스상에서 방화벽을 설정하는 도구로서 커널 2.4 이전 버전에서 사용되던 ipchains를 대신하는 방화벽 도구이다. iptables는 커널상에서의 netfilter 패킷필터링 기능을 사용자 공간에서 제어하는 수준으로 사용할 수 있다.
    > 

UFW의 기본 정책(default policy)은 아래와 같다.

- 들어오는 패킷(incoming): deny
- 나가는 패킷(outgoing): allow

## 2.1. UFW 사용법 (man ufw)

### 설치

```bash
# UFW 설치
apt install ufw
```

### 상태(활성 여부) 확인

```bash
# UFW 상태 확인
ufw status
# 디테일하게 확인 (verbose: 장황한)
ufw status verbose
```

### 기본 정책 설정

`ufw default allow|deny|reject [incoming|outgoing|routed]`

```bash

# UFW incoming 기본 정책 허용
ufw default allow
ufw default allow incoming

# UFW incoming 기본 정책 차단
ufw default deny
ufw default deny incoming

# UFW outgoing 기본 정책 허용 및 차단
ufw default allow outgoing
ufw default deny outgoing
```

### 허용 룰 추가

`ufw allow <port>/<optional: protocol>`

```bash
# 22번 포트 허용
ufw allow 22

# 22번 포트 tcp 허용
ufw allow 22/tcp
```

### 거부 룰 추가

`ufw deny <port>/<optional: protocol>`

```bash
# 22번 포트 거부
ufw deny 22

# 22번 포트 udp 거부
ufw deny 22/udp
```

### 룰 삭제

```bash
# 22번 포트 tcp에 대한 거부 룰 삭제
ufw delete deny 22/tcp
```

### service 명을 이용한 설정

```bash
# 서비스명 확인
less /etc/services

# 서비스명으로 허용/거부 룰 추가
ufw allow <service name>
ufw deny ssh
```

### 애플리케이션 프로필을 이용한 설정

```bash
# 현재 사용 가능한 프로필
ufw app list

# 애플리케이션 프로필명으로 허용/거부 룰 추가
ufw allow "SSH"
```

### UFW 로그 기록

```bash
ufw logging on
ufw logging off
```

### 특정한 IP주소 허용/거부

```bash
ufw allow from <ip address>

# 192.168.0.100 에서 접속 허용
ufw allow from 192.168.0.100
```

---

# 참고

[방화벽 (네트워킹) - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EB%B0%A9%ED%99%94%EB%B2%BD_(%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%82%B9))

[UFW - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/UFW)

[[Ubuntu] 우분투 방화벽(UFW) 설정](https://webdir.tistory.com/206)

[UFW Essentials: Common Firewall Rules and Commands | DigitalOcean](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

[Debian 9에서 UFW로 firewall 설정하는 방법](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jodi999&logNo=221409997866)