#### spark application의 핵심 구성

- 사용자의 main function을 실행하고 클러스터 환경에서 `parallel operations`를 수행하는 `driver program`로 구성



#### 공유변수

- broadcast
  - 모든 노드들의 메모리에 값을 캐시
- accumulators
  - counters나 sums 처럼 오직 추가 기능을 하는 변수



#### SparkConf

- Spark application의 설정을 담당

  - `ket-value` 형태로 spark 환경변수를 설정

  - set 메소드를 사용

    ```scala
    def set(key: String, value: String): SparkConf = {
        set(key, value, false)
      }
    ```

- unit test시에는 `new SparkConf(false)` 를 사용하여 외부 설정을 skip하여 사용할 수 있다.

  - system properties와 같은 설정으로 생성

- 생성 예시

  ```scala
  val conf = new SparkConf().setMaster("local").setAppName("app Name")
  ```

  - app name은 cluster UI에 나타나는 application의 이름이다.

    

#### SparkContext

- spark의 함수적 기능의 main entry

- Spark Cluster의 연결을 관리

- 클러스터에 RDDs, accumulators, broadcast 변수를 생성

- JVM에 오직 1개의 SparkContext가 존재할 수 있다.

  - 새로운 SparkContext를 생성하려면 기존의 Sparkcontext를 `stop()` 메소드로 멈춰야 한다.

- **`SparkConf` 객체를 먼저 만들어야 한다.**

  - ```scala
    val conf = new SparkConf().setAppName(appName).setMaster(master)
    val sc = new SparkContext(conf)
    ```



#### Spark RDD

- 특징

  - 변경 불가능 (Read-only)
  - 여러 노드에 분산되어 있다.

- 클러스터 노드들에 파티션(분할)되어 있고 병렬로 작동될 수 있는 collection

  - 파티션 : *a logical chunk of a large data set*
  - 병렬적으로 실행가능한 `fault-tolerant`인 collection
    - `fault-tolerant` 하다? : 클러스터의 노드에서 데이터 처리 중간에 fault가 발생하는 경우 spark는 rdd를 메모리에 올려 사용하기 때문에 fault 가 발생한 데이터를 replicating, checkpointing 할 필요 없다.
    - 부모로부터 해당 데이터(rdd)가 어떻게 만들어 졌는지 lineage(저용량)를 기록하여 언제든지 또 만들 수 있다.
    - DAG를 활용 (Lineage 계보를 디자인 한 그래프)
  - spark는 rdd를 메모리에 올려 효율적으로 재사용할 수 있다. 메모리의 rdd는 `read-only`이다.

- 생성 방법

  - driver program에 있는 기존의 collection을 병렬화

    ```scala
    val conf = new SparkConf().setAppName(appName).setMaster(master)
    val sc = new SparkContext(conf)
    val rddData = sc.parallelize(data)
    ```

  - 외부 저장소에서 data를 참조하여 생성

    - 로컬 파일 시스템, HDFS, Cassandra, HBase, Amazon S3 등 Hadoop이 지원하는 모든 스토리지 소스에서 분산 데이터 세트를 생성 할 수 있다 .

    - 텍스트 파일 예시

      ```scala
      val textFileRdd = sc.textFile("data.txt")
      ```



#### Types of RDD

- RDD는 스칼라의 추상 클래스이다.
- **ParallelCollectionRDD** 
  - An RDD created by Spark Context through parallelizing an existing collection. 
  - Eg. sc.parallelize, sc.makeRDD
- **CoGroupedRDD** 
  - An RDD that cogroups its parents. 
  - For each key k in parent RDDs, the resulting RDD contains a tuple with the list of values for that key.
- **HadoopRDD** 
  - An RDD that provides core functionality for reading data stored in HDFS using the older MapReduce API. 
  - The most notable use case is the return RDD of SparkContext.textFile.
- **MapPartitionsRDD** 
  - An RDD that applies the provided function to every partition of the parent RDD. 
  - A result of calling operations like map, flatMap, filter, mapPartitions, etc.
- **CoalescedRDD** 
  - a result of repartition or coalesce transformations.
- **ShuffledRDD** 
  - a result of shuffling, 
  - e.g. after repartition or coalesce transformations.
- **PipedRDD** 
  - an RDD created by piping elements to a forked external process.
- **SequenceFileRDD** 
  - an RDD that can be saved as a SequenceFile.



#### RDD Operator

- Transformation : 데이터의 흐름, lineage를 만든다.
- Action : Transformation으로 작성된 lineage를 따라 데이터를 처리하여 결과를 생성 (lazy-evaluation)
- ![img](https://t1.daumcdn.net/cfile/tistory/256F5B4D56BDCC2307)

- Narrow dependency

  -  네트워크를 안타고 메모리의 속도로 동작해서 빠르다
  -  파티션이 부셔져도 해당 노드에서 바로 복원 가능하다
  -  map, filter, union, join with inputs co-partitioned

  Wide dependency

  -  네트워크의 속도로 동작해서 느리다
  -  노드끼리 셔플이 일어나야 한다
  -  파티션이 부셔지면 계산비용이 비싸다
  -  groupByKey, join with inputs not co-partitioned




#### 트랜스포메이션

- 데이터를 변경하는 방법
  - 스파크의 핵심 데이터 구조는 불변성이다.
- 스파크에서 비즈니스 로직을 표현하는 핵심 개념
- 좁은 의존성
  - 각 입력 파티션이 하나의 출력 파티션에만 영향을 미친다.
  - 파이프라이닝을 자동으로 수행
    - 좁은 의존성을 지정하는 경우 모든 작업이 메모리에서 일어난다.
- 넓은 파티션
  - 하나의 입력 파티션이 여러 출력 파티션에 영향을 미친다.
  - 셔플 작업 수행
    - 셔플의 결과를 디스크에 저장한다.

#### 지연 연산

- 스파크가 연산 그래프를 처리하기 직전까지 기다리는 동작 방식
- 스파크는 특정 연산 명령이 내려진 즉시 데이터를 수정하지 않고 원시 데이터에 적용할 트랜스포메이션의 실행 계획을 생성
- 트랜스포메이션을 사용해 논리적 실행 계획을 세운다.
  - 지향성 비순환 그래프(DAG) 생성
  - 지향성 비순환 그래프의 각 단계는 불변성을 가진 신규 DataFrame을 생성한다.

#### 액션

- 실제 연산을 수행
- 트랜스포메이션으로부터 결과를 계산하도록 지시하는 명령
- 액션을 지정하면 스파크 잡이 시작된다.
  - 스파크 잡은 좁은 트랜스포메이션를 수행한 후 파티션별로 레코드 수를 넓은 트랜스포메이션한다.



#### 파티셔닝

- RDD의 파티션수를 잘 설정해야 한다.

  - Spark는 클러스터 기반으로 파티션수를 자동으로 설정한다.
  - 일반적으로 클러스터의 각 cpu마다 2~4개의 partition이 필요하다.
  - shuffling overhead를 줄여야 하기 때문에 적절한 설정이 필요하다.
  - spark는 `partitioner` property를 사용하여 RDD의 특정 record를 저장할 worker들을 결정한다.
    - `partitioner`가 `NONE`일 경우 무작위로 worker 노드를 선택하여 저장

- 파티셔닝에 영향을 끼치는 요소

  - | 요소                               | 내용                                                         |
    | ---------------------------------- | ------------------------------------------------------------ |
    | Available Resources                | task를 실행할수 있는 core의 개수                             |
    | External Data Sources              | Local collections ,Cassandra table or HDFS file 의 크기가 partition 수를 결정한다. |
    | Transformations used to derive RDD | RDD에서 또다른 RDD를 생성할 때 파티션 수를 결정.             |

- 단위 stage의 task의 개수 = 파티션의 개수

- partitioning behavior

  | API Call                                               | partitions.size                                              | partitioner                                          |
  | ------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------------- |
  | sc.parallelize(…)                                      | sc.defaultParallelism                                        | NONE                                                 |
  | sc.textFile(…)                                         | sc.defaultParallelism or number of file blocks , whichever is greater | NONE                                                 |
  | filter(),map(),flatMap(),distinct()                    | same as parent RDD                                           | NONE except filter preserve parent RDD’s partitioner |
  | rdd.union(otherRDD)                                    | rdd.partitions.size + otherRDD. partitions.size              | NONE except filter preserve parent RDD’s partitioner |
  | rdd.intersection(otherRDD)                             | max(rdd.partitions.size, otherRDD. partitions.size)          | NONE except filter preserve parent RDD’s partitioner |
  | rdd.subtract(otherRDD)                                 | rdd.partitions.size                                          | NONE except filter preserve parent RDD’s partitioner |
  | rdd.cartesian(otherRDD)                                | rdd.partitions.size * otherRDD. partitions.size              | NONE except filter preserve parent RDD’s partitioner |
  | reduceByKey(),foldByKey(),combineByKey(), groupByKey() | same as parent RDD                                           | HashPartitioner                                      |
  | sortByKey()                                            | same as parent RDD                                           | RangePartitioner                                     |
  | mapValues(),flatMapValues()                            | same as parent RDD                                           | parent RDD’s partitioner                             |
  | cogroup(), join(), ,leftOuterJoin(), rightOuterJoin()  | depends upon certain input properties of two RDDs involved.  | HashPartitioner                                      |

- `repartition()` vs `coalesce()`

  - RDD를 생성한 뒤 filter()연산을 비롯한 다양한 트랜스포메이션 연산을 수행하다 보면 최초에 설정된 파티션 개수가 적합하지 않은 경우가 발생할 수 있다.

    이 경우 coalesce()나 repartition()연산을 사용해 현재의 RDD의 파티션 개수를 조정할 수 있다.

  - `repartition()` 메소드는 매우 비용이 비싼 메소드이다.

    - full shuffle 을 하기 때문
    - 새로운 partition을 생성한다. (파티션 수를 늘리거나 줄일수 있다.)

    예시

    ```scala
    Node 1 = 1,2,3
    Node 2 = 4,5,6
    Node 3 = 7,8,9
    Node 4 = 10,11,12
    
    ======After repartition()=======
    
    Node 1 = 1,2,3,10,11,12
    Node 3 = 7,8,9,4,5,6
    ```

  - `coalesce()`는 데이터의 이동을 최소화 한다.

    - 셔플을 강제적으로 하지 않는다.
    - 좁은 의존성을 초래하는 방식

    예시

    ```scala
    Node 1 = 1,2,3
    Node 2 = 4,5,6
    Node 3 = 7,8,9
    Node 4 = 10,11,12
    
    ======After coalesce()=======
    
    Node 1 = 1,2,3 + (10,11,12)
    Node 3 = 7,8,9 + (4,5,6)
    ```

---

#### Job

- Partition으로 구성된 RDD로부터 Transformation을 적용한 후 Action의 결과를 얻어내기 위해 DAGScheduler에 넘겨진 Action Item이다.
  - DAGScheduler는 RDD Lineage를 분석해서 Stage로 표현된 물리적 실행 계획을 만든다.
  - DAGScheduler는 logical execution plan을 physical execution plan으로 변환한다.
- Action의 결과를 얻어내기 위해서 해야하는 일

#### Stage

- Stage란 DAGSchedualr에 의해서 생성된 물리적 실행 계획의 단계(Step)들이다. 
  - 하나의 Stage를 처리하기 위해서는 하나 이상의 Task가 필요하다.
- Spark Job은 1개 이상의 Stage로 구성되어 있다.
- 최종 Stage는 여러 개의 ResultTask들로 이루어져있고 그 이전 Stage는 ShuffleMapTasks로 구성
- Stage가 나눠지는 기준은 shuffle dependencies에 의해 정해진다.
  - shuffle을 발생시키는 연산(`join`, `repartition`)과 같은 연산이 Stage를 나누는 기준

#### Task

- Task란 어떤 Stage 가 처리되기 위해서 계산되어야 하는 partition에 대한 작업으로 executor에 의해 처리된다.
  - RDD의 각 파티션을 처리하기 위한 가장 작은 개별 실행(Execution) 단위
- Spark는 각각의 파티션을 처리할 Task를 하나씩 만들고, 각 Task는 클러스터에 하나의 코어를 요청
  - 생성된 Task는 worker노드의 executor로 분배
- ShuffleMapTask : Task를 실행시킨 후 Task의 결과를 여러 개의 Bucket으로 나눈다. (다음 Task를 사용하기 위해)
  - ex) map, reduce
- ResultTask : Task를 실행시킨 후 Task 결과를 Driver Application으로 전송
  - ex) Action 함수들

---

#### 출처

- https://brocess.tistory.com/183
- https://medium.com/parrot-prediction/partitioning-in-apache-spark-8134ad840b0
- https://ourcstory.tistory.com/139
- https://blog.knoldus.com/rdd-sparks-fault-tolerant-in-memory-weapon/
- https://pubdata.tistory.com/29
- https://rtfmplz.github.io/2017/05/02/what-is-job-stage-task-in-spark

