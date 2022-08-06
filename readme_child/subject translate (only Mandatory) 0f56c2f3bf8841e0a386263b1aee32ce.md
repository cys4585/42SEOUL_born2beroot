# subject translate (only Mandatory)

## Introduction

- 특정한 지침(specific instructions) 하에, VirtualBox에서 first machine을 만들 것이다. 그러면 프로젝트가 끝날 때, 엄격한 규칙을 구현하면서 자신만의 OS를 설정할 수 있을 것이다.

## General guidelines

- VirtualBox를 사용해야 한다. (불가능하다면 UTM 사용)
- 레포지토리의 최상단에 `signature.txt` 파일만 제출해야 한다. `signature.txt` 파일에 machine의 virtual disk의 signature(서명)을 복사해야 한다.

## Mandatory Part

- 이 프로젝트는 특정한 룰을 따라 첫 서버를 set up(설정)하는 것으로 구성된다.
    
    (Warning) 서버를 설정하는 문제이기 때문에, 최소한의 서비스를 install 해야 한다. 이러한 이유로, graphical interface는 여기에선 쓸모가 없다. 그러므로 [X.org](http://X.org) 또는 기타 다른 동등한 그래픽 서버를 install하는 것은 금지된다. 그렇지 않으면, 점수는 0점이 될 것이다.
    
- Debian의 최신&안정 버전 or CentOS의 최신&안정 버전 중 하나를 Operating System으로 선택해야 한다. 만약 시스템 관리를 처음 한다면, Debian이 강력 추천된다.
    
    (Information) CentOS를 설정하는 것은 매우 복잡하다. 그러므로, KDump를 설정할 필요가 없다. 그러나, SELinux는 시작할 때 실행중 이어야 하며 해당 구성은 프로젝트의 필요에 맞게 조정되어야 한다. Debian을 위한 AppArmor 또한 시작 시 실행중 이어야 한다.
    
- LVM을 사용해 적어도 2개의 암호화된 파티션을 생성해야한다. 아래는 예상되는 partitioning의 한 예시이다.
    
    ![Untitled](subject%20translate%20(only%20Mandatory)%200f56c2f3bf8841e0a386263b1aee32ce/Untitled.png)
    
    (Information) 디펜스 동안, 선택한 operating system에 대한 몇 가지 질문을 받을 것이다. 예를 들어, aptitude와 apt의 차이점, 또는 SELinux or AppArmor가 무엇인지 알아야 한다. 한 마디로, 사용하는 것을 이해해라!
    
- SSH 서비스는 오직 4242포트 위에서만 실행될 것이다. 보안상의 이유로, SSH를 사용하여 root로 연결하는 것은 불가능하다.
    
    (Information) SSH 사용은 디펜스 동안에 새로운 계정을 설정함으로써 테스트될 것이다. 그러므로 SSH가 어떻게 작동 하는지 이해해야만 한다.
    
- operating system을 UFW 방화벽으로 구성해야하므로 4242 포트만 열려 있어야 한다.
    
    (Information) 방화벽은 virtual machine을 시작할 때 활성화되어있어야 한다. CentOS의 경우 기본 방화벽 대신 UFW를 사용해야 합니다. 그것을 설치하려면 아마도 DNF가 필요할 것이다.
    
    - virtual machine의 hostname은 ‘your login ending with 42’이어야만 한다 (예: wil42). 평가를 받는 동안에 이 hostname을 수정해야할 것이다.
    - 강력한 패스워드 정책을 구현해야 한다.
    - 엄격한 규칙에 따라 **sudo**를 설치하고 구성해야 한다.
    - root 유저에 더하여, 루트 사용자 외에도 username으로 로그인한 유저가 존재해야 한다.
    - 이 유저는 **user42**와 **sudo** groups에 속해야 한다.
    
    (Information) 디펜스 동안, 새로운 유저를 생성해야 하고, group에 assign(할당)해야 한다.
    
- 강력한 패스워드 정책을 설정하기 위해, 다음의 요구사항을 준수(comply with)해야 한다.
    - 패스워드는 30일마다 만료(expire)돼야 한다.
    - 암호를 수정하기 전에 허용되는 최소 일 수는 2일로 설정될 것이다.
    - 유저는 패스워드가 만료(expire)되기 7일 전에 경고 메세지를 받아야 한다.
    - 패스워드는 최소 10글자 이상이어야 한다. 비밀번호는 대문자와 숫자를 포함(contain)해야 한다. 또한, 비밀번호는 연속적으로(consecutive) 동일한(identical) 문자를 3자를 초과하여 포함해서는 안된다.
    - 패스워드는 유저의 name을 포함해선 안된다.
    - 다음의 규칙은 root 패스워드에는 적용(apply)되지 않는다: 패스워드는 이전(former) 패스워드의 일부가 아닌 7글자 이상을 가져야 한다.
    - 당연히, root 패스워드는 이 정책을 준수(comply with)해야 한다.
        
        (Warning) configuration files를 설정한 후에, root 계정을 포함해, virtual machine에 존재(present)하는 계정의 모든 패스워드를 변경해야 할 것이다.
        
- **sudo** group에 대한 강력한 구성을 설정하기 위해, 다음의 요구사항을 준수(comply with)해야 한다.
    - sudo를 사용한 인증(authentication)은 비밀번호가 잘못되었을 경우 3회의 시도 까지로 제한되어야 한다.
    - **sudo** 사용 시 잘못된 비밀번호로 인한 오류가 발생(occur)할 경우 원하는 커스텀 메시지가 표시(display)되어야 합니다.
    - **sudo**를 사용한 각 작업(action)은 입력과 출력 모두 보관(archive)되어야 한다. log file은 **/var/log/sudo/** 폴더에 저장되어야 한다.
    - 보안상의 이유로 **TTY** 모드를 활성화해야 한다.
    - 보안상의 이유로 **sudo**에서 사용할 수 있는 경로또한 제한되어야 합니다.
    예시: **/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin**
- 마지막으로, **monitoring.sh**라 불리는 간단한 script를 생성해야 한다. **bash**에서 개발되어야 한다.
- 서버가 시작시, script는 10분마다 모든 터미널에 일부 정보(아래 목록)를 표시할 것이다 (take a look at **wall**). 배너는 선택사항이다. 오류가 보여서는 안된다.
- script는 항상 다음의 정보를 표시할 수 있어야 한다.
    - operating system과 kernel 버전의 아키텍처
    - physical processors의 수
    - virtual processors의 수
    - 서버에서 현재 가용(available) RAM과 사용률(utilization rate)
    - 서버에서 현재 가용 memory와 사용률
    - processors의 현재 사용률
    - 마지막으로 재부팅한 날짜와 시간
    - LVM이 활성 상태인지 여부
    - 활성화된 연결의 수
    - 서버를 사용하는 유저의 수
    - 서버의 **IPv4** 주소와 **MAC (Media Access Control)** 주소
    - **sudo** 프로그램으로 실행된 명령의 수
    
    (Information) 디펜스 동안, script가 어떻게 동작하는지 설명하라는 요청을 받을 것이다. 또한 script를 수정하지 않고 중단해야 할 것이다. **cron**을 살펴봐라.
    
- 이것은 script의 예상 작동 방법의 예시이다.
    
    ![Untitled](subject%20translate%20(only%20Mandatory)%200f56c2f3bf8841e0a386263b1aee32ce/Untitled%201.png)
    
- 아래는 subject의 요구사항의 일부를 확인하는 데 사용할 수 있는 두 명령이다.
    - For CentOS:
        
        ![Untitled](subject%20translate%20(only%20Mandatory)%200f56c2f3bf8841e0a386263b1aee32ce/Untitled%202.png)
        
    - For Debian:
        
        ![Untitled](subject%20translate%20(only%20Mandatory)%200f56c2f3bf8841e0a386263b1aee32ce/Untitled%203.png)
        

## Submission

- Git repository의 루트에 `signature.txt` 파일만을 제출해야한다. 시스템의 가상 디스크의 서명(signature)을 여기에 붙여넣어야 한다. 이 서명(signature)을 얻으려면, 먼저 기본 설치 폴더(VM이 저장된 폴더)를 열어야 한다.
    - MacOS: `~/VirtualBox VMs/`
- 그런 다음 가상 시스템의 `.vdi` 파일에서 `sha1` 형식(format)으로 서명을 검색합니다. 다음은 `centos_serv.vdi` 파일에 대한 명령 예제입니다.
    - MacOS: `shasum centos_serv.vdi`
    - This is an example of what kind of output you will get:
        - 6e657c4619944be17df3c31faa030c25e43e40af

(Information) 가상 시스템의 서명이 변경될 수 있습니다. 당신의 첫 번째 평가 후에. 이 문제를 해결하려면 가상 시스템을 복제하거나 저장 상태를 사용할 수 있습니다.

물론 Git 저장소에 가상 머신을 제출하는 것은 금지되어 있습니다. 디펜스 동안에, `signature.txt`파일의 서명(signature)은 가상 머신의 파일과 비교될 것이다. 만약 둘 다 똑같지 않다면, 당신의 성적은 0이 될 것입니다.