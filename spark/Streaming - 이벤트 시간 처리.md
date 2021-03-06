#### 이벤트 시간 처리

- 이벤트가 생성된 시간을 기준으로 정보를 분석해 늦게 도착한 이벤트까지 처리할 수 있게 해준다.
- 핵심 아이디어
  - 잡의 전체 생명주기 동안 관련 상태를 유지
- 스파크의 DStream API는 이벤트 시간과 관련된 처리 정보를 제공하지 않는다.

#### 스트림 처리 시스템의 이벤트 처리 시간 종류

- 이벤트가 실제로 발생한 시간(이벤트 시간)
- 이벤트가 시스템에 도착한 시간 또는 처리된 시간(처리 시간)

#### 이벤트 시간

- 데이터에 기록되어 있는 시간
- 이벤트가 실제로 일어난 시간
- 이벤트를 다른 이벤트와 비교하는 더 강력한 방법을 제공
- 문제점
  - 지연되거나 무작위로 도착하는 이벤트를 해결해야 한다는 문제점
- 스트림 처리 시스템은 지연되거나 무작위로 도착한 데이터를 반드시 제어할 수 있어야 한다.

#### 처리시간

- 스트림 처리 시스템이 데이터를 실제로 수신한 시간
- 외부 시스템에서 제공하는 것이 아닌 스트리밍 시스템이 제공하는 속성이므로 순서가 뒤섞이지 않는다.

---

#### 상태 기반 처리

- 오랜 시간에 걸쳐 중간 처리 정보(상태)를 사용하거나 갱신하는 경우에만 필요

- 이벤트 시간을 사용하거나 키에 대한 집계를 사용하는 상황에서 일어난다.

- 스파크는 상태 기반 연산에 필요한 복잡한 처리를 대신한다.

  - 사용자는 단순하게 로직만 정의하면 된다.

- 스파크는 상태 기반 연산에 필요한 중간 상태 정보를 상태 저장소에 저장

  - 스파크는 상태 저장소의 구현체인 인메모리 상태 저장소를 제공
  - 인메모리 상태 저장소는 중간 상태를 체크포인트 디렉터리에 저장해 내고장성을 보장한다.

  

#### 임의적인 상태 기반 처리

- 상태의 유형, 갱신 방법 그리고 제거 시점에 따라 세밀한 제어가 필요한 경우 사용
- 사용자는 스틀미 처리에 필요한 모든 정보를 스파크에 저장할 수 있다.



#### 상태 기반 처리 예시

- 특정 키의 개수를 기반으로 윈도우 생성하기
- 특정 시간 범위 안에 일정 개수 이상의 이벤트가 있는 경우 알림 발생시키기
- 결정되지 않은 시간 동안 사용자 세션을 유지하고 향후 분석을 위해 세션 저장하기

#### 임의적인 상태 기반 처리 예시

- 데이터의 각 그룹에 맵 연산을 수행하고 각 그룹에서 최대 한 개의 로우를 만들어낸다.
  - `mapGroupsWithState API`를 이용
- 데잍의 각 그룹에 맵 연산을 수행하고 각 그룹에서 하나 이상의 로우를 만들어낸다.
  - `flatMapGroupsWithState  API`를 이용

---

#### 이벤트 시간 윈도우

- 이벤트 시간 분석의 첫 단계는 타임스탬프 컬럼을 적절한 스파크 SQL 타임스템프 데이터 타입으로 변환해야 한다.

---

#### 워터마크로 지연 데이터 제어하기

- 워터마크 
  - 특정 시간 이후에 처리에서 제외할 이벤트나 이벤트 집합에 대한 시간 기준이다.
- 얼마나 늦게 도착한 데이터까지 받아들일지 기준을 정해야 한다.
  - 워터마크 또는 데이터가 필요 없어지는 시간을 지정하지 않으면 스파크는 중간 결과 데이터를 영원히 저장한다.
- 스트림에서 오래된 데이터를 제거하는 데 필요한 워터마크를 반드시 지정해야 한다.

---

#### 스트림에서 중복 데이터 제거하기

- 레코드 단위 처리 시스템에서 가장 처리하기 어려운 연산 중 하나이다.
- 중복을 찾기 위해서는 여러 레코드를 반드시 한 번에 처리해야 한다.
  - 한 번에 많은 레코드를 처리해야 하므로 중복 제거는 처리 시스템에 큰 부하를 발생
- 구조적 스트리밍은 최소 한 번 처리하는 방식을 제공하는 메시지 시스템을 쉽게 사용할 수 있다.
  - 스파크는 데이터 중복을 제거하기 위해 사용자가 지정한 키를 유지 하면서 중복 여부를 확인한다.