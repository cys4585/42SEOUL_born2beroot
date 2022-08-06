# apt, aptitude (패키지 관리 도구)

# 리눅스 패키지 및 패키지 관리

## 리눅스 패키지(Linux Package)

 리눅스 패키지란 리눅스 시스템에서 소프트웨어를 실행하는데 필요한 파일들(실행 파일, 설정 파일, 라이브러리 등)이 담겨있는 설치 파일 묶음이다. 패키지의 종류는 소스 패키지(Source Package)와 바이너리 패키지(Binary Package)가 있다.

## 소스 패키지(Source Package) vs 바이너리 패키지(Binary Package)

- 소스 패키지는 말 그대로 소스 코드가 들어있는 패키지로 컴파일 과정을 통해 바이너리 파일로 만들어야 실행할 수 있다. 즉, 소스 패키지는 설치할 때 컴파일 작업도 진행되므로 설치 시간이 길고 컴파일 작업 과정에서 오류가 발생할 수 있다.
- 바이너리 패키지는 성공적으로 컴파일된 바이너리 파일이 들어있는 패키지이다. 이미 컴파일이 되어 있으니 소스 패키지에 비해 설치 시간도 짧고 오류가 발생할 가능성도 적다. 리눅스의 기본 설치 패키지들은 대부분 바이너리 패키지이다.

### 바이너리 패키지의 문제점

 바이너리 패키지는 컴파일이 되어있어서 내 컴퓨터 환경과 바이너리 패키지가 컴파일된 환경이 달라서 문제가 발생할 수 있다. 즉, 내가 설치한 바이너리 패키지를 실행하기 위해서 특정 버전의 라이브러리들이 필요하다면 내가 그 라이브러리들을 가지고 있지 않을 경우 제대로 프로그램을 실행할 수 없다. 이렇게 바이너리 패키지를 제대로 실행하기 위해서는 특정 라이브러리나 다른 패키지가 필요할 수 있는데, 이러한 패키지 간의 의존성을 **패키지 의존성(Package Dependencies)**이라 부른다.

## 리눅스 패키징 방식(Linux Packaging System)

1. Debian 계열(Debian, Ubuntu, Linux Mint 등)
    - 패키징 방식: DEB
    - 패키지 파일 확장자: `*.deb`
2. RedHat 계열(Redhat, Fedora, CentOS 등)
    - 패키징 방식: RPM
    - 패키지 파일 확장자: `*.rpm`

## 리눅스 패키지 관리 도구(Linux Package Management Tool)

 리눅스에서는 패키지를 관리하기 위해 패키지 관리 도구를 사용한다. 패키지 관리 도구는 저수준 패키지 도구(Low Level Package Tool)과 고수준 패키지 도구(High Level Package Tool)가 있다.

### 저수준 패키지 도구

 패키지 파일을 설치하거나 제거할 때 사용하는 도구이다. 각 패키지의 파일을 설치하고 제거할 수는 있지만, 패키지 간의 의존성을 알지 못한다. Debian 계열에선 dpkg이 있고, RedHat 계열에선 rpm이 있다.

### 고수준 패키지 도구

 고수준 패키지 도구는 패키지 파일을 설치하고 제거할 뿐만 아니라 다운로드 가능한 **패키지들을 검색**하고 **패키지 의존성(Packaged Dependencies)을 해결**하는 도구이다. 패키지 의존성을 해결한다는 의미는 해당 패키지가 가지고 있는 패키지 간의 의존성을 파악하고 필요하다면 자동으로 설치해준다는 뜻이다. Debian 계열에서 사용하는 고수준 패키지 도구는 apt-get, apt, aptitude이고, RedHat 게열에서 사용하는 고수준 패키지 도구는 yum, dnf이다. 

## 리눅스 패키지 저장소(Linux Package Repository)

 리눅스 패키지 도구(고수준 패키지 도구)를 사용해서 원하는 소프트웨어를 다운로드할 수 있다. 예를 들어, Debian에서 apt를 사용해서 아래의 명령어를 통해 python3 패키지를 설치할 수 있다.

```bash
sudo apt install python3
```

 이 때, apt가 python3 패키지를 설치할 수 있는 이유는 apt가 인터넷에 있는 특정 주소로부터 필요한 패키지를 가져오기 때문이다. 이렇게 패키지들을 가지고 있는 사이트들을 리눅스 패키지 저장소(Linux Package Repository)라 부른다. 리눅스 패키지 저장소는 패키지에 대한 정보(패키지들의 이름, 버전 번호, 패키지 설명 등)가 포함된 Metadata를 가지고 있다. 이를 통해 패키지 도구를 이용해 저장소가 가지고 있는 패키지 정보를 확인할 수 있다.

```bash
apt list
```

 리눅스는 패키지 저장소 사이트들을 특정 파일에 저장하고 있는데, Debian의 경우 `/etc/apt/sources.list` 파일에 저장되어 있다.

## 리눅스 패키지 설치 과정

![[https://itsfoss.com/package-manager/](https://itsfoss.com/package-manager/)](apt,%20aptitude%20(%E1%84%91%E1%85%A2%E1%84%8F%E1%85%B5%E1%84%8C%E1%85%B5%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%20%E1%84%83%E1%85%A9%E1%84%80%E1%85%AE)%20fb3c86a579874c30be657bcec32eb371/Untitled.png)

[https://itsfoss.com/package-manager/](https://itsfoss.com/package-manager/)

1. 패키지 관리자는 패키지 저장소로부터 패키지 정보를 가진 메타데이터를 로컬 캐시에 저장한다.
2. 패키지 관리자가 특정 패키지의 설치를 명령하면 로컬 캐시를 참조하여 패키지 정보를 찾고 패키지 저장소를 통해 패키지를 다운로드한다.
3. 이 때 패키지 의존성이 있다면, 패키지 관리자는 종속성을 처리하고 필요한 패키지들을 자동으로 설치한다.

---

# apt vs aptitude

## Debian 패키지 관리 도구

  Debian 시스템에서 패키지를 관리하는 도구는 다양하지만, 주요 도구는 아래와 같다.

1. dpkg
    - `.deb` 패키지의 설치와 삭제를 담당
    - dpkg로 패키지를 설치할 때는 `.deb` 설치파일이 PC에 존재하고 있어야함
    - 패키지 간의 의존성 문제를 해결해주지 못함
    - 다른 고수준 패키지 관리 도구 내부에서 실질적으로 사용됨
        - 만약 apt-get을 통해 패키지를 설치할 경우, 실질적인 설치는 저수준 패키지 도구인 dpkg가 담당하며, apt-get은 의존성 관계 등을 처리함
2. apt-get
    - dpkg를 사용하여 패키지 검색, 설치 및 제거를 위한 패키지 관리 도구
    - 저장소의 url이 `/etc/apt/sources.list`에 작성되어 있다면 인터넷을 통해 해당 저장소에서 파일을 다운로드해서 설치
    - 패키지 설치, 삭제 시 의존성을 해결
3. apt-cache
    - 패키지를 검색하기 위한 패키지 관리 도구
4. apt
    - apt-get / apt-cache 등에서 가장 널리 사용되는 기능들을 모아놓은 패키지 관리 도구
    - 보다 사용자에게 친화적인 방법으로 패키지 처리 방법을 제공
        - 패키지를 설치/제거하는 동안 진행률 표시줄을 보여줌
        - 업그레이드 해야하는 패키지 목록을 보여줌
        - 출력 메세지가 상세함
    - apt-get과 유사한 명령 옵션이 있지만, apt-get과 역호환되지는 않음
    - 기능이 제한적임
5. aptitude
    - 사용자 인터페이스를 추가하여 사용자가 대화식으로 패키지를 검색, 설치 및 제거할 수 있도록 하는 고급 패키지 도구 프론트엔드
    - 텍스트 기반 대화형 UI 모드와 명령줄 비대화형 모드 모두에서 작동 가능

## apt(apt-get, apt-cache 포함)와 aptitude의 차이점

1. apt는 다른 상위 수준 패키지 관리자에서 사용할 수 있는 하위 수준 패키지 관리자이고 aptitude는 상위 수준 패키지 관리자이다. (비교적)
2. aptitude는 apt보다 기능면에서 더 **광범위**하다.
3. aptitude가 apt보다 **사용자 친화적**이다. (텍스트 전용 대화형 UI)
4. 패키지 설치, 제거 및 **충돌** 해결과 관련된 상황에서 더 나은 가치를 제공한다.
    - 설치된 패키지를 제거할 때, aptitude는 사용하지 않는 패키지를 자동으로 제거하지만, apt-get은 `-auto-remove` 옵션을 추가하거나 `apt-get autoremove`를 지정하여 사용자가 이를 직접 명시해야한다.
    - 특정 작업이 **차단되는 이유 또는 특정 작업을 수행해야 하는 이유**를 알려주기 위한 `why`, `why-not` 커멘드를 제공한다.
    - apt-get은 패키지 설치나 제거와 관련하여 충돌하는 경우 메시지와 함께 작업이 중단되는 반면, aptitude는 **충돌 해결을 위한 조치를 제안**할 수 있다.
    - aptitude는 apt-cache보다 패키지 검색에 있어 훨씬 쉽고 효율적인 방법을 제공한다.

---

## 참고

[리눅스 패키지(Linux Package)에 대한 이해](https://bradbury.tistory.com/227)

[ko/PackageManagement/PkgTools - Debian Wiki](https://wiki.debian.org/ko/PackageManagement/PkgTools)

[What is APT and Aptitude? and What's real Difference Between Them?](https://www.tecmint.com/difference-between-apt-and-aptitude/)

[Difference Between APT, APT-GET, APT-CACHE and APT-CONFIG - GeeksforGeeks](https://www.geeksforgeeks.org/difference-between-apt-apt-get-apt-cache-and-apt-config/)

[Difference Between apt and apt-get Explained - It's FOSS](https://itsfoss.com/apt-vs-apt-get-difference/)

[apt-get 와 dpkg의 차이점](https://velog.io/@broccoliindb/apt-get-%EC%99%80-dpkg%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)

[apt vs apt-get 차이](https://yooloo.tistory.com/50)