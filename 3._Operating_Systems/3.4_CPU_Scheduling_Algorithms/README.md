# 3.4 CPU Scheduling Algorithms

- [3.4.1 비선점형 방식(non-preemptive)](#341-비선점형-방식non-preemptive)
  - [FCFS (First Come First Served)](#fcfs-first-come-first-served)
  - [SJF (Shortest Job First)](#sjf-shortest-job-first)
  - [우선순위](#우선순위)
- [3.4.2 선점형 방식(preemtive)](#342-선점형-방식preemtive)
  - [라운드로빈(RR, Round Robin)](#라운드로빈rr-round-robin)
  - [SRF(Shortest Remaining Time Frist)](#srfshortest-remaining-time-frist)
  - [다단계 큐](#다단계-큐)


<br/>

---

- CPU 스케쥴러는 CPU 스케쥴링 알고리즘에 따라 프로세스에서 해야하는 일을 스래ㅔ드 단위로 CPU에게 할당함.
- 프로그램이 실행 될 때 CPU 스케쥴링 알고리즘이 어떤 프로그램에게 소유권을 줄지 결정한다.
  - CPU 이용률은 높도록
  - 주어진 시간에 일을 많이하도록
  - ready queue 에 있는 프로세스는 적도록
  - 응답시간은 짧도록

# 3.4.1 비선점형 방식(non-preemptive)
- 프로세스가 스스로 CPU 소유권을 포기하는 방식
- 강제로 프로새스를 중지하지 않는다. 컨텍스트 스위칭 부하가 적다.

## FCFS (First Come First Served)
- 가장 먼저 온 것을 가장 먼저 처리
- 긴 수행시간의 프로세스가 앞에 있다면, convoy effect (ready queue에 오래 대기하는 현상)이 발생할 수 있다.

## SJF (Shortest Job First)
- 실행히간이 가장 짧은 프로세스를 가장 먼저 실행
- 긴 시간이 걸리는 프로세스가 실행되지 않는 현상(starvation)이 일어남
- 평균 대기시간이 가장 짧음
- 하지만 실제로는 실행시간을 알 수 없기 떄문에, 과거 실행기록을 토대로 추측한다.

## 우선순위
- 기존 SJF 스케줄은 starvation이 일어나기 떄문에, 오래된 작업일수록 우선순위를 높이는 방법(aging)을 통해 보완한 방식


<br/>

---

# 3.4.2 선점형 방식(preemtive)
- 현대 운영체제가 쓰고 있는 방식
- 알고리즘에 의해 지금 사용중인 프로세스를 중단시키고, 강제로 다른 프로세스에게 CPU 소유권을 할당

## 라운드로빈(RR, Round Robin)
- priority scheduling의 일종으로, 동일한 시간을 각 프로세스에게 주고 시간안에 끝내지 못하면 다시 ready queue에 넣는 알고리즘
  - 할당시간이 김 : FCFS가 됨
  - 할당시간이 짧음 : 컨텍스트 스위칭이 잦아서 오버헤드가 발생
- 로드밸런서에서 트래픽 분산 알고리즘으로 쓴다.

## SRF(Shortest Remaining Time Frist)
- SJF와 달리 중간에 더 짧은 작업이 들어오면 수행하던 프로세스를 중지하고 해당 프로세스를 수행하는 알고리즘

## 다단계 큐
- 우선순위에 따른 ready queue를 여러 개 사용하고 queue 마다 다른 스케쥴링 알고리즘 적용
- 큐 간의 프로세스 이동이 안되서 스케줄링 부담이 적으나 유연성이 떨어진다.