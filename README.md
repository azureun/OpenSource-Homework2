# 📑[리눅스 명령어 설명] ps, top, kill, jobs

## 1. 프로세스 정보 확인
### ▶️ ps (프로세스 출력)
- 현재 시스템에서 실행 중인 프로세스에 관한 정보를 출력.
- `$ ps [option]`

#### [옵션]
> **기본 프로세스 출력**
- a : 터미널과 연관된 프로세스만 출력
- x : 터미널과 연관되지 않는 프로세스만 출력
- -A, -e : 모든 프로세스 출력
- -a : 세션 리더와 커미널과 연관되지 않은 프로세스를 제외하고 모든 프로세스를 출력

> **지정한 프로세스 출력**
- p : 지정한 PID(Process ID) 목록의 정보만 출력
- -C : 지정한 프로세스의 실행 파일 이름의 정보만 출력
- -u : 특정 사용자의 프로세스 정보를 출력

> **프로세스 표시 형식**
- u : 프로세스의 소유자 정보를 함께 출력
- l : BSD 형식(Berkeley Software Distribution)의 긴 형식으로 출력
- e : 프로세스 정보 & 프로세스의 환경 정보 출력
- -l : 긴 포멧으로 출력
- -o : 사용자 정의 형식 지정 가능

> **프로세스 장식**
- f : 프로세스 계층을 텍스트 형식의 트리구조 보여줌.
- -f : 전체 포맷으로 출력

💡 ps 명령어는 -(dash)가 안 들어간 옵션이 있음. 전통 유닉스 System V와 BSD로 나뉨.
> - System V : - 사용<br>
> - BSD : - 사용하지 않음.

#### [프로세스 도표 항목과 의미]
![alt 프로세스 항목](/img/process.png)
- F : 프로세스 플래그
- S : 프로세스 상태코드
- UID : 프로세스 소유자 이름
- PID : 프로세스 고유식별자
- PPID : 부모프로세스의 PID
- C : 프로세서 사용률 (%로 표기)
- PRI : 프로세스의 우선순위. 높은 값이 낮은 우선순위
- NI : nice 값. 19~20
- SZ : 프로세스 이미지가 차지하는 물리적 페이지 크기
- WCHAN : 대기중일때 커널 함수의 이름
- STIME : 프로세스가 시작한 시간
- TTY : 터미널의 종류
- TIME : 총 CPU 사용시간
- CMD : 프로세스의 실행 시 명령줄

#### [ps 옵션 조합 예시 : aux/-ef]
1. `ps aux`
   - 프로세스 상태를 보는 용도
```BASH
$ ps aux                 # 모든 터미널의 프로세스 소유자 정보와 함께 프로세스 정보를 출력.
$ ps aux | grep apache   # 특정 프로세스(apache)만 출력
```
![alt aux](/img/aux.png)

2. `ps -ef`
   - 부모 프로세스와 자식 프로세스의 관계를 보는 용도
```BASH
$ ps -ef                  # 현재 실행중인 모든 프로세스의 정보를 전체 포맷으로 출력
$ ps -ef | more           #모든 프로세스의 full 포맷으로 보여줌. more 명령어로 페이지 단위로 출력.
$ ps -ef | grep apache    # 모든 프로세스의 출력값을 grep을 이용하여 apache가 포함된 라인들 출력.
```
![alt ef](/img/ef.png)

### ▶️ top (실시간 프로세스 출력)
- 현재 시스템에서 실행 중인 프로세스에 관한 정보를 **실시간**으로 출력.
- `$ top [option]`
#### [옵션]
- -n : 지정한 숫자만큼 화면 출력을 갱신
- -u : 지정한 사용자의 프로세스를 모니터링
- -b : 출력결과를 파일이나 다른 프로그램으로 전달
- -d : 화면 갱신 주기를 초 단위로 설정
- -p : 지정한 PID 프로세스를 모니터링
---
#### [top 명령어 출력 구조]
- 시스템에 대한 전반적인 요약
![alt system_sum](/img/system_sum.png)
> - 1st line : 현재시간, 서버 가동 후 유지시간, 현재 접속 사용자, 최근 1,5,15분 동안 시스템 부하
> - 2nd line : 프로세스 상태(총 프로세스, 실행중, sleep, stop, 좀비 프로세스)
> - 3rd line : cpu 상태
> - 4th line : MEM 상태
> - 5th line : swap memory 상태
- cpu를 사용하는 순으로 프로세스들 정렬해서 보여줌.
![alt cpu_use](/img/cpu_use.png)
> - PR : 우선순위
> - NI(nice value) : 20~19 사이의 수. 값이 작을수록 우선순위 높음.
> - VIRT : 작업에 사용된 가상 메모리 총 사용량
> - RES : 프로세스가 사용하는 실제 메모리양
> - S : 현재 프로세스의 상태 나타냄.
> - TIME+ : 프로세스가 시작하여 사용한 CPU 시간

## 2. 프로세스 제어
### ▶️ kill (시그널, 프로세스 종료)
- 불필요한 프로세스, 잘못 실행된 프로세스를 죽임
- ```BASH
  $ kill [options] [pid]

  $ kill -9 PID    #PID를 시그널 번호 9(KILL) 전송해서 죽임
  $ kill -TERM -1  # 자신이 실행한 모든 프로세스를 종료
  ```
![alt kill](/img/kill.png)

#### [옵션]
- -s <signal> : 특정 시그널을 사용해 프로세스 종료. 기본적으로 SIGTERM 시그널이 사용됨.
- -l, --list : 지원되는 시그널 목록 출력.
- -a, --all : 현재 사용자에 속한 모든 프로세스 종료.
- -q, --queue : 프로세스에 시그널을 보내는 대신 시그널을 대기열에 추가.

#### [시그널 번호별 의미]
번호|시그널|의미
:---:|:---:|:---
1|SIGUP|터미널에서 접속이 끊겼을때 보내지는 시그널. 변화된 내용을 적용하기 위해 재시작 할 때 사용.
2|SIGINT|인터럽트 시그널로 실행 중지시킴. Ctrl+c 입력시 보내짐.
3|SIGQUIT|실행중지 시그널로 Ctrl+\ 입력시 보내짐.
9|SIGKILL|프로세스를 강제로 종료시키는 시그널
15|SIGTERM|kill의 기본 시그널. 정상 종료시키는 시그널
18|SIGCONT|시그널에 의해 정지된 프로세스를 다시 실행시키는 시그널
19|SIGSTOP|정지 시그널
20|SIGTSTP|일지정지 시키는 시그널. Ctrl+z 입력시 보내짐.


## 3. 프로세스 전환
### ▶️ jobs (백그라운드 프로세스 출력)
- 현재 돌아가고 있는 백그라운드 프로세스 리스트를 모두 출력.
- `$ jobs [options] [job name or number]`

<img src="/img/jobs.png" alt="jobs" width="340px" height="200px">

#### [옵션]
- -l : 각 작업에 대한 추가 정보(작업 번호, 현재 작업, 프로세스 그룹 ID, 상태, 작업을 시작한 명령) 제공.
- -n : 최종 통지된 이후 중지 or 종료 작업만 표시
- -p : 선택된 작업에 대한 프로세스 그룹 리더의 프로세스 ID를 표시

---
**[참고]**
- [Inpa Dev](https://inpa.tistory.com/entry/LINUX-%F0%9F%93%9A-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B4%80%EB%A6%AC-%EB%AA%85%EB%A0%B9%EC%96%B4-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-Foreground-Background "InpaDev")
- [gr-st-dev](https://gr-st-dev.tistory.com/210 "gr-st")
- [IBM](https://www.ibm.com/docs/ko/aix/7.2?topic=j-jobs-command "IBM")
- [zetawiki](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_jobs "zetawiki")
  

