# cron & monitoring.sh

# 1. cron

## 1.1. cron

소프트웨어 유틸리티 cron은 유닉스 계열 컴퓨터 운영체제의 시간 기반 잡 스케줄러이다. 소프트웨어 환경을 설정하고 관리하는 사람들은 작업을 고정된 시간, 날짜, 간격에 주기적으로 실행할 수 있도록 스케줄링하기위해 cron을 사용한다.

## 1.2. crontab

cron은 셸 명령어들이 주어진 일정에 주기적으로 실행하도록 규정해놓은 crontab(cron table) 파일에 의해 구동된다. crontab 파일들은 잡 목록 및 cron 데몬에 대한 다른 명령들이 보관된 위치에 저장되어 있다. 사용자들은 자신들만의 crontab 파일들을 가질 수 있으며, 가끔은 /etc 또는 /etc 하위 디렉토리에 시스템 관리자들만이 편집할 수 있는, 시스템 전반에 영향을 미치는 crontab 파일이 존재하는 경우도 있다.

![Untitled](cron%20&%20monitoring%20sh%209323e72a233d42a1bb7cce706c61efb9/Untitled.png)

### 1.2.1. crontab 설정 방법

1. `/etc/crontab` 파일 직접 편집 → 비추(작동하지 않는 경우가 있다고 함)
2. `crontab -u root -e` 명령어로 스케줄 등록, 편집
    - `-u`: crontab을 사용하거나 수정할 사용자의 이름을 지정
    - `-e`: crontab을 편집
    - `crontab -d`: 등록된 스케줄 삭제
    - `crontab -l`: 등록된 스케줄 목록 출력
    - `crontab -r`: 현재 사용자가 등록한 crontab 전체 삭제

### 1.2.2. crontab 설정 사례

- `분 시 일 월 요일 command`
    - 분: 0 ~ 59
    - 시: 0 ~ 23
    - 일: 1 ~ 31
    - 월: 1 ~ 12 OR jan, feb, mar, apr, …
    - 요일: 0 ~ 6 (Sunday=0 or 7) OR sun, mon, tue, wed, thu, fri, sat
- `* * * * * bash ~/monitoring.sh`: 최상단 디렉토리의 monitoring.sh 스크립트 파일을 1분에 한 번씩 실행
- `0 5 * * * bash ~/monitoring.sh`: 매일 5시에 실행
- `5 * * * * bash ~/monitoring.sh`: 매시 5분에 실행
- `0 5 1 * * bash ~/monitoring.sh`: 매월 1일 5시에 실행
- `*/5 * * * * bash ~/monitoring.sh`: 5분마다 실행
- `0 */5 * * * bash ~/monitoring.sh`: 5시간마다 실행
- `0 5,23 * * * bash ~/monitoring.sh`: 매일 5시, 23시에 실행
- `0 5,23 * * 0,3 bash ~/monitoring.sh`: 매주 일요일, 수요일 5시, 23시에 실행

---

# 2. monitoring.sh

예시

![Untitled](subject%20translate%20(only%20Mandatory)%200f56c2f3bf8841e0a386263b1aee32ce/Untitled%201.png)

## 2.1. monitoring.sh

경로: `/root/monitoring/monitoring.sh`

```bash
#! /bin/bash

# operating system과 kernel 버전의 아키텍처
echo -n "#Architecture : "
uname --all

# physical processors의 수
echo -n "#CPU physical : "
nproc

# virtual processors의 수
echo -n "#vCPU : "
cat /proc/cpuinfo | grep processor | wc -l

# 서버에서 현재 가용(available) RAM과 사용률(utilization rate)
echo -n "#Memory Usage : "
free --mega | awk 'NR==2 { printf "%d/%dMB (%.2f%%)\n", $3, $2, ($3 * 100) / $2}'

# 서버에서 현재 가용 memory와 사용률 (Disk)
echo -n "#Disk Usage : "
df -B m | grep -E 'root|home' | awk '{ total+=$2 } { used+=$3 } END { printf "%.1f/%.1fGB (%d%%)\n", used/1024, total/1024, (used * 100) / total }'

# processors의 현재 사용률
echo -n "#CPU load : "
mpstat | grep all | awk '{ using = 100 - $13 } { printf "%.1f%%\n", using }'

# 마지막으로 재부팅한 날짜와 시간
echo -n "#Last boot : "
who -b | awk '{ printf "%s %s\n", $3, $4 }'

# LVM이 활성 상태인지 여부
echo -n "#LVM use : "
lvm_number=$(lsblk | awk '{ print $6 }' | grep 'lvm' | wc -l)
if [ ${lvm_number} -gt 0 ]
then
	echo "yes"
else
	echo "no"
fi

# 활성화된 연결의 수
echo -n "#Connections TCP : "
ss | grep 'tcp' | wc -l | tr '\n' ' ' && echo "ESTABLISHED"

# 서버를 사용하는 유저의 수
echo -n "#User log : "
who --count | grep "# users=" | cut -d '=' -f2

# 서버의 IPv4 주소와 MAC (Media Access Control) 주소
echo -n "Network : "
echo -n "IP " && hostname -I | tr -d '\n' && ip link | grep link/ether | awk '{ printf "(%s)\n", $2}'

# sudo 프로그램으로 실행된 명령의 수
echo -n "Sudo : "
journalctl _COMM=sudo | grep COMMAND | wc -l | tr '\n' ' ' && echo "cmd"

```

- `monitoring.sh` 사용 명령어 정리
    - `#! /bin/bash`
        - 스크립트의 제일 앞에서 이 파일이 어떤 명령어 해석기의 명령어 집합인지를 시스템에게 알려주는 역할을 한다.
        - `#!` 은 두 바이트의 매직 넘버(magic number)로서, 실행 가능한 쉘 스크립트라는 것을 나타내는 표시자이다.
        - `#!`  바로 뒤에 나오는 것은 경로명으로, 스크립트에 들어있는 명령어들을 해석할 프로그램의 위치를 나타내는데 그 프로그램이 쉘인지, 프로그램 언어인지, 유틸리티인지를 나타낸다.
    - `echo`
        - `echo`는 string을 출력하고 자동으로 개행을 출력한다.
        - `-n`: 개행을 제거한다.
    - `uname`
        - print system information
        - `-a, --all`: print all information, in the following order, except omit -p and -i if unknown:
    - `nproc`
        - print the number of processing units available
    - `command1 | command2`
        - command1의 결과(output)을 command2의 입력(input)으로 전달
    - `grep`
        - print lines that match patterns
        - `grep string`: string과 매칭되는 라인 출력
        - 조건 사용
            - AND: `grep string1 | grep string2`
            - OR: `grep -E 'string1|string2'`
            - NOT: `grep -v string`
        - ~로 시작/ ~로 끝나는 라인
            - ‘/dev’로 시작하는 라인 그랩: `grep '^/dev'`
            - ‘/boot’로 끝나는 라인 그랩: `grep '/boot$'`
    - `wc`
        - print newline, word, and byte counts for each file
        - `l`: print the newline counts
    - `free`
        - Display amount of free and used memory in the system
        - `--mega`: Display the amount of memory in megabytes
    - `awk`
        - pattern scanning and text processing language
        - raw → 레코드 → NR → `NR==2` 이면, 2번 째 레코드(2번 째 라인)
        - col → 필드 → $ → `$3` 이면, 3번째 필드
        - 필드의 개수: `NF`
        - `printf`: C 언어의 포맷과 같다.
        - 특정 필드의 합
            - `awk '{sum2+=$2} {sum3+=$3} END {printf "%d, %d\n", sum2, sum3}' [FILE]`
    - `df`
        - report file system disk space usage
        - `-h, --human-readable`: 사람이 읽기쉽도록 KB, MB, GB 단위로 출력
        - `-B, --block-size=SIZE`: SIZE = {k, m, g, t … } 등 지정한 단위로 출력
            - `df -B m` → MB 단위로 출력
    - `top`
        - The `top` program provides a dynamic real-time view of a running system.
        - `-b`
            - 배치 모드 (일괄 처리)
            - 다른 프로그램 또는 파일로 출력을 보내는 데 유용
        - `-n`
            - 종료하기 전에 생성해야 하는 최대 반복 횟수 또는 프레임 수 지정
        - 필요한 부분을 `awk` 로 가져오기 어려워서 다른 방법을 찾기로 함
    - `mpstat`
        - report processors related statistics.
        - `sysstat` 패키지 설치
        - `idle`: %로 표현. 사용하고 있지 않는 비율을 의미함
    - `last reboot`
        - 마지막으로 리부트한 시간이 언제인지 desc 정렬하여 출력
    - `who`
        - Print information about users who are currently logged in.
        - `-a, --all`: all information
        - `-b, --boot`: time of last system boot
        - `-q, --count`: all login names and number of users logged on
    - `wc`
        - print newline, word, and byte counts for each file
        - `-l, --lines`: print the newline counts
    - `ss`
        - 소켓을 조사하는 유틸리티
        - `-t, --tcp`: Display TCP sockets
        - `-a, --all`: Display both listening and non-listening (for TCP this means established connections) sockets.
    - `tr [OPTIONS]... SET1 [SET2]`
        - translate or delete characters
        - `-d, --delete`: delete characters in SET1, do not translate
    - `who`
        - print information about users who are currently logged in
    - `cut`
        - print selected parts of lines from each FILE to standard output
        - `-d, --delimiter`: 딜리미터(구분문자)를 기준으로 문자열을 자른다
        - `-f, --fields`: 자른 문자열 중 선택할 필드를 지정한다
    - `hostname`
        - show or set the system's host name
        - `-I, --all-ip-addresses`: display all network addresses of the host
    - `ip`
        - show / manipulate routing, network devices, interfaces and tunnels
        - `link`: network device
        - link/ether 가 mac addr임
    - `journalctl`
        - systemd-journald.service가 작성한 systemd 저널의 내용을 쿼리하는 데 사용
        - 'adm', 'systemd-journal' 그룹의 사용자는 모든 메시지를 볼 수 있음
        - 모든 메시지를 보기 위해 `adm`, `systemd-journal` 그룹의 사용자로 등록해야함
            - `adduser <user> <group>`
        - `_COMM`: 원하는 커맨드 로그만 볼 수 있음

## 2.2. crontab

```bash
crontab -u root -e
```

```bash
*/10 * * * * bash /root/monitoring/monitoring.sh | wall
```

- `wall`
    - write a message to all users
    - 현재 로그인한 모든 사용자의 단말기에 메시지 또는 파일의 내용 또는 표준 입력을 표시합니다. 이 명령은 79자보다 긴 행을 줄 바꿈합니다. 짧은 줄은 공백으로 패딩되어 79자로 되어 있습니다. 명령은 항상 캐리지 리턴과 새 라인을 각 줄의 끝에 배치합니다.
    - 슈퍼유저만이 메시지 거부를 선택했거나 메시지를 자동으로 거부하는 프로그램을 사용하고 있는 사용자의 단말기에 글을 쓸 수 있다.
    - 호출자가 슈퍼유저가 아니고 프로그램이 set-user-ID 또는 set-group-ID일 경우 파일에서 읽기가 거부됩니다.

---

# 참고

[cron - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/Cron)

[[총정리] Crontab 사용법](https://cloud-oky.tistory.com/320)

[](https://happist.com/553442/%EC%84%9C%EB%B2%84%EC%97%90%EC%84%9C-%EC%9E%90%EB%8F%99-%EC%8B%A4%ED%96%89%EC%9D%84-%EA%B0%80%EB%8A%A5%EC%BC%80-%ED%95%B4%EC%A3%BC%EB%8A%94-crontab%ED%81%AC%EB%A1%A0%ED%83%AD-%EC%84%A4%EC%A0%95#5_keulontaeb_seoljeong_salye)

[서버 자동 실행 crontab(크론탭) 설정 방법 및 문제점 해결하기 | 꿈꾸는섬](https://happist.com/553442/%EC%84%9C%EB%B2%84%EC%97%90%EC%84%9C-%EC%9E%90%EB%8F%99-%EC%8B%A4%ED%96%89%EC%9D%84-%EA%B0%80%EB%8A%A5%EC%BC%80-%ED%95%B4%EC%A3%BC%EB%8A%94-crontab%ED%81%AC%EB%A1%A0%ED%83%AD-%EC%84%A4%EC%A0%95#5_keulontaeb_seoljeong_salye)

[[Linux] Redirect('>')와 Pipe('|')의 차이](https://twpower.github.io/133-difference-between-redirect-and-pipe)

[리눅스 awk 명령어 사용법. (Linux awk command) - 리눅스 파일 텍스트 데이터 검사, 조작, 출력.](https://recipes4dev.tistory.com/171)

[[Linux] grep 명령어에서 AND, OR, NOT 조건 사용하기](https://twpower.github.io/173-grep-and-or-not)

[[Linux] top 명령어로 서버의 상태 파악하기](https://sabarada.tistory.com/146)

[리눅스 부팅시각 확인](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B6%80%ED%8C%85%EC%8B%9C%EA%B0%81_%ED%99%95%EC%9D%B8)

[[Shell Script] if ... else 조건문](https://brownbears.tistory.com/221)

[Bash Shell - 문자열 자르기 (substring, split)](https://codechacha.com/ko/shell-script-substring/)

[리눅스 journalctl](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_journalctl)