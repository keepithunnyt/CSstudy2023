# 3.2 Memory

- [3.2.1 메모리 계층](#321-메모리-계층)
  - [캐시 (cache)](#캐시-cache)
  - [캐시 히트와 캐시 미스](#캐시-히트와-캐시-미스)
- [3.2.2 메모리 관리](#322-메모리-관리)
  - [가상 메모리(virtual memory)](#가상-메모리virtual-memory)
  - [스레싱(thrashing)](#스레싱thrashing)
  - [메모리 할당](#메모리-할당)
  - [페이지 교체 알고리즘](#페이지-교체-알고리즘)

<br/>

---

# 3.2.1 메모리 계층
- 레지스터, 캐시, 메모리, 저장장치로 구성. 계층이 위로 올라갈수록 가격이 비싸고, 용량은 작아지고, 속도는 빨라진다. (RAM 16GB와 SSD 16GB)
  - **레지스터** : CPU 안의 작은 메모리. 휘발성이며 속도가 가장 빠르고 용량이 가장 적음
 - **캐시** : L1, L2 (요즘은 L3도 있음) 캐시를 지칭. 휘발성, 속도 빠름, 기억용량이 적음.
 - **주 기억장치** : RAM을 가리킴. 휘발성, 보통 속도, 보통의 용량
   - 하드디스크로부터 일정량의 데이터를 복사해 임시저장함. 필요시 CPU에 빠르게 전달한다.
 - **보조 기억장치** : HDD, SSD를 일컬음. 비휘발성, 속도 낮음, 용량이 많음.

<br/>

---

## 캐시 (cache)
- 미리 복사해놓는 임시 저장소, 속도가 다른 장치 간 병목현상을 줄이기 위한 메모리.
- 계층과 계층 사이에 있는 계층을 캐싱 계층이라고 함.

### 지역성의 원리
- 캐시 계층을 두지 않고 직접 캐시 설정을 하려면 자주 사용하는 데이터 기반으로 설정한다.
- **지역성** : 자주 사용하는 데이터의 근거. 시간 지역성(temporal locality)과 공간 지역성(spatial locality)으로 나뉜다.
  - **시간 지역성** : 최근에 사용한 데이터에 다시 접근하려는 특성
  - **공간 지역성** : 최근 접근한 데이터를 이루고 있는 공간이나, 그것과 가까운 공간에 접근하려는 특성

<br/>

---
## 캐시 히트와 캐시 미스
 - 캐시 히트 : 제어장치가 캐시에서 원하는 데이터를 찾음
   - CPU 내부 서브 기반으로 동작하여 빠름.
 - 캐시 미스 : 해당 데이터가 캐시에 없어서 주 메모리로 가서 데이터를 찾아옴.
   - 시스템 버스 기반이라 느림

### 캐시 매핑
- 캐시가 히트되기 위해 매핑하는 방법. CPU의 레지스터와 RAM(주메모리) 간 데이터 송수신을 기반으로 설명됨.

|이름|설명|
|---|---|
|직접 매핑(directed mapping)|메모리가 1~100 이고 캐시가 1~10이면 1:1~10, 2:1~20 으로 매핑하는 것.<br/> 속도 빠름. 충돌 잦음.|
|연관 매핑(associative mapping)|순서X. 관련있는 캐시와 메모리를 매핑함.<br/> 속도 느림. 충돌 적음.|
|집합 연관 매핑(set associative mapping)|직접 매핑과 연관 매핑을 합친 방법. 순서는 일치하지만 집합을 둬서 블록화되어 있어서 검색 효율이 좋다.<br/> 메모리가 1~100 이고 캐시가 1~10이면 캐시 1~5에 1~50의 데이터를 무작위로 저장.|

### 웹 브라우저의 캐시 매핑
- 쿠키, 로컬 스토리지, 세션 스토리지 등이 있음.
- 사용자 커스텀 정보나 인증 모듈 사항을 웹 브라우저에 저장해서, 추후 서버에 요청할 때 중복 요청 방지에 쓰임.

|쿠키|로컬 스토리지|세션 스토리지|
|---|---|---|
|- 만료기한이 있는 key-value 저장소<br/> - same site 옵션을 strict로 설정하지 않으면 다른 도메인에서 요청 시 자동 전송됨.<br/> - 4KB까지 데이터 저장 가능. 만료기한을 정할 수 있음(보통 서버에서 정한다.)<br/> -  document.cookie로 쿠키를 볼 수 없게 httponly 옵션을 거는 것이 중요하다.|- 만료기한이 없는 key-value 저장소<br/> - 10MB까지 저장<br/>  - 웹 브라우저를 닫아도 유지됨<br/>  - 도메인 단위로 저장, 생성됨<br/>  - HTML5를 지원하지 않는 웹 브라우저에서 사용X<br/>  - 클라에서만 수정가능| - 만료기한이 없는 key-value 저장소<br/> - 탭 단위로 세션 스토리지 생성<br/> - 5MB까지 저장<br/> - HTML5를 지원하지 않는 웹 브라우저에서 사용X<br/>  - 클라에서만 수정가능|

### 데이터베이스의 캐싱 계층
- 레디스(redis) 데이터베이스를 캐싱 계층으로 두고 성능을 향상시키기도 한다.
  - [레디스 --> 앱] 캐시 히트 : 레디스로부터 데이터를 읽는다.
  - [앱 --> 레디스] 데이터를 레디스에 씀.
  - [메인 DB --> 앱] 캐시 미스 : 메인 데이터베이스로부터 데이터를 가져옴



<br/>

---
# 3.2.2 메모리 관리
## 가상 메모리(virtual memory)
- 컴퓨터가 실제로 이용 가능한 메모리 자원을 추상화하여 사용자에게 매우 큰 메모리로 보이게 하는 것.
  - 가상 주소(logical address) : 가상으로 주어진 주소. 메모리 관리장치(MMU)에 의해 실제 주소로 변환.
  - 가상 주소(physical address) : 실제 메모리상 주소
- **페이지 테이블** : 가상 주소와 실제 주소가 매핑되어있고 프로세스의 주소 정보가 들어있어서 가상 메모리를 관리한다.
  - 속도 향상을 위해 TLB를 사용한다.
  - **TLB** : 메모리와 CPU 사이에 있는 주소 변환용 캐시. 페이지 테이블에 있는 리스트를 보관하며, CPU가 페이지 테이블까지 가지 않도록 해서 속도를 향상시킬 수 있는 캐시 계층이다.

### 스와핑 (swapping)
- 당장 사용하지 않는 영역을 하드디스크로 옮기고, 하드디스크의 일부분을 메모리처럼 불러와 쓰는 것
- 페이지 폴트가 일어나지 않은 것처럼 만든다.

### 페이지 폴트(page fault)
- 가상 메모리에는 존재하지만 실제 메모리(RAM)에 존재하지 않는 데이터나 코드에 접근할 떄 발생
- 페이지 폴트와 스와핑 과정
  1. CPU는 물리 메모리를 확인해서 해당 페이지가 없으면 트랩(3.1.2 인터럽트 페이지 확인!)을 발생시켜 OS에 알림
  2. OS는 CPU 동작을 일시정지함.
  3. OS는 페이지 테이블을 확인하여 가상 메모리에 페이지가 존재하는지 확인
     - 없으면 프로세스를 중단하고 현재 물리 메모리에 비어있는 프레임이 있는지 확인.
     - 물리 메모리에도 없으면 **스와핑 발동!**
  4. 비어있는 프레임에 해당 페이지를 로드하고 페이지 테이블 최신화
  5. 중단한 CPU 동작 재개 

- **페이지(page)** : 가상 메모리를 사용하는 최소 크기 단위
- **프레임(frame)** : 실제 메모리를 사용하는 최소 크기 단위

<br/>

---

## 스레싱(thrashing)
- 메모리의 페이지 폴트율이 높은 것을 의미 (컴퓨터 성능 저하를 불러온다.)
- 메모리에 너무 많은 프로세스가 동시에 올라가면 스와핑이 많이 일어나서 발생한다.
  - 페이지 폴트가 많으면 CPU 이용률이 낮아짐
  - CPU 이용률이 낮으면 OS가 CPU가 한가한 줄 알고, CPU 가용성을 더 높이기 위해 더 많은 프로세스를 메모리에 올리게 된다.
- 해결법 : 메모리 늘리기, HDD를 SSD로 변경
- OS단에서 해결법
  - **작업 세트(work set)** : 지역성을 통해 결정된 페이지 집합을 만들어서 미리 메모리에 로드하는 것.<br/> 미리 로드하면 탐색 비용 절감 및 스와핑 감소 효과가 있다.
  - **PFF(Page Fault Frequency)** : 상한선과 하한선을 만듬.<br/> 상한선에 도달하면 프레임을 늘리고 하한선에 도달하면 프레임을 줄이는 방법.

<br/>

---

## 메모리 할당
- 프로그램에 메모리 할당 시 시작 메모리 위치, 메모리의 할당 크기 기반으로 할당함.

### 연속 할당
- 메모리에 연속적으로 공간을 할당함.
- **고정 분할 방식(fixed partition allocation)**
  - 메모리를 미리 나누어 관리하는 방법
  - 융통성이 없고 내부 단편화 발생
- **가변 분할 방식(variable partition allocation)**
  - 프로그램의 크기에 맞게 동적으로 메모리를 나눠 사용
  - 외부 단편화는 발생할 수 있음.

|이름|설명|
|---|---|
|최초 적합(first fit)|위쪽이나 아래쪽부터 시작해서 홀을 찾으면 바로 할당|
|최적 적합(best fit)|프로세스 크기 이상인 공간 중 가장 작은 홀 부터 할당|
|최악 적합(worst fit)|프로세스 크기와 가장 많이 차이나는 홀에 할당|

- **내부 단편화(internal fragmentation)** : 메모리를 나눈 크기보다 프로그램이 작아서, 남아도는 낭비 공간이 발생하는 현상
- **외부 단편화(external fragmentation)** : 메모리를 나눈 크기보다 프로그램이 커서 들어가지 못하는 공간이 많이 발생하는 현상.
- **홀(hole)** : 할당할 수 있는 비어있는 메모리 공간

### 불연속 할당
- 메모리를 연속적으로 할당하지 않음.
- **페이징(paging) 기법** : 동일한 크기의 페이지 단위(주로 4KB)로 나누어 서로 다른 메모리 위치에 프로세스를 할당.<br/> 홀의 크기가 균일하지 않은 문제가 없어지지만 주소 변환이 복잡하다.
- **세그멘테이션(segmentation) 기법** : 의미 단위인 세그먼트(segment)로 나누는 방식
  - 프로세스를 이루는 메모리는 코드 영역, 데이터 영역, 스택 영역, 힙 영역으로 이뤄진다.
  - 이를 코드와 데이터로 나누거나 코드 내의 작은 함수를 세그먼트로 놓고 나눌 수 있음
  - 공유와 보안에 유리하지만 홀 크기가 균일하지 않는 단점이 있다.
- **페이지드 세그멘테이션(paged segmentation) 기법** : 프로그램을 세그먼트 단위로 나누어 공유, 보안에 강점을 두고, 동일한 크기의 페이지 단위로 나누는 것.

<br/>

---

## 페이지 교체 알고리즘
- 페이지 교체 알고리즘 기반으로 스와핑이 발생한다.

### 오프라인 알고리즘(offline algorithm)
- 미래에 참조될 페이지와 현재 할당하는 페이지를 바꿔쓰는 알고리즘
- 가장 좋지만 미래를 알 수 없어서 사용할 수 없다.
- 다른 알고리즘과의 성능 비교에 대한 상한 기준(upper_bound)을 제공한다.
---
- **FIFO(First In First Out)** : 가장 먼저 온 페이지를 교체 영역에 가장 먼저 놓는 방법
- **LRU(Least Recently Used)** : 가장 오랫동안 참조되지 않은 페이지를 교체. 오래된 것을 알기 위해 각 페이지마다 계수기, 스택을 두어야하는 문제점이 있다.
- 해시테이블(unordered_map)과 이중 연결리스트(list)를 사용.
- **NUR(Not Used Recently)** : LRU에서 발전함
    - clock 알고리즘
    - 0과 1을 가진 비트를 둔다.
    - 1은 최근에 참조되었고 0은 참조되지 않음을 의미한다.
    - 시계방향으로 돌면서 0을 찾는다.
    - 0을 찾은 순간 해당 프로세스를 교체하고 해당 부분을 1로 바꾼다.
-  **LFU(Least Frequently Used)** : 가장 참조 횟수가 적은 페이지를 교체한다.

<br/>

---