#### Spark streaming

- DStream API
  - 마이크로 배치 방식
  - 이벤트 시간 처리 지원 x
- 구조적 스트리밍 API
  - 최적화 기술, 이벤트 시간 처리, 연속형 처리 지원

---

#### DStream API

- 시간별로 도착한 데이터들의 연속적인 모음

  - 내부적으로 각각의 DStream은 각 시간별로 도착한 RDD들의 연속적인 모음으로 구성된다.

    ![Spark Streaming](https://spark.apache.org/docs/latest/img/streaming-dstream.png)

- 외부의 입력 소스로부터 만들 수 도 있지만, 다른 DStream에 트랜스포메이션을 적용해서 만들 수 있다.

- Data source

  - Flume
  - Kafka
  - HDFS

- 연산

  - 트랜스포메이션 : 새로운 DStream을 만든다.
  - 결과 연산 : 외부 시스템에 데이터를 써주는 연산

---

#### Spark Streaming

- `SparkConf` 인스턴스를 생성하고 스파크 스트리밍의 핵심인 `StreamingContext`를 생성한다.

  - 각 입력 소스마다 스파크 스파크 스트리밍은 `receiver`를 실행
    - 애플리케이션의 익스큐터들 안에서 데이터를 모으고 RDD에 저장하는 태스크
    - 데이터를 받아서 장애 대응을 위해 다른 익스큐터에 복제
  - `setMaster`에서 local 스레드 설정을 실행중인 receiver의 수보다 높게 설정해야 한다. 
  - 만약 `local[1]`로 설정하면 1개의 스레드로 receiver 역할도 하고 데이터를 처리할 수 없다.

  ```scala
  // SparkConf 인스턴스 생성
  val conf = new SparkConf().setMaster("local[*]").setAppName("test")
  // StreamingContext 생성,  파라미터 : (SparkConf, 마이크로배치 시간)
  val ssc = new StreamingContext(conf, Seconds(1))
  ```

- 체크포인팅

  - 파일 시스템에 주기적으로 상태를 저장하는 매커니즘

    - 일반적으로 데이터의 5~10개 배치 묶음마다 체크포인팅을 설정

  - ```scala
    ssc.checkpoint("체크포인팅 경로")
    ```

---

#### Spark Streaming using DStream 흐름

- `SparkConf`, `StreamingContext` 인스턴스 생성
- `DStream` 데이터 소스 설정
- `transformation`과 `output operations`를 사용하여 논리적인 코드 작성
- 실시간 프로세스 실행  : `ssc.start()`
- 프로세스 종료를 대기 : `ssc.awaitTermination()`
- 종료 요청으로 실시간 프로세스 종료 : `ssc.stop()`

---

#### Word Count 예제

```scala
// ip와 port를 socketTextStream의 파라미터로 넘겨주어 DStream을 생성한다.
val lines = ssc.socketTextStream("localhost", 9999)

// DStream의 RDD를 flatMap으로 트랜스포메이션
val words = lines.flatMap(_.split(" "))

// 위에서 설정한 time에 맞게 batch 연산 
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)

// 결과 출력
wordCounts.print()

// 논리적 연산을 다 정의하면 real processing을 실행해야 한다.
ssc.start()

// 종료 요청이 발생하기 전까지 프로세스 종료를 하지 않고 기다린다.
ssc.awaitTermination()
```

![Spark Streaming](https://spark.apache.org/docs/latest/img/streaming-dstream-ops.png)

---

#### DStream point

- `Streaming Context`는 1개만 존재할 수 있다.
- `Context`가 stop 되면 다시 restart 할 수 없다.
- `StreamingContext`의 `stop()` 메소드는 `SparkContext`까지 종료한다.
  - `stop()` 파라미터로 boolean 값을 주어 `SparkContext` 종료 여부 설정
- `SparkContext`는 여러 `StreamingContext`에서 사용될 수 있다.

---

#### Spark Streaming source

- Basic source
  - `StreamingContext` API를 사용하여 직접 사용 가능
  - file system, socket connection
- Advanced sources
  - spark 연산을 하기 위해 추가적인 `linkin`이 필요하다.
  - Kafka, Flume, Kinesis 등

---

#### DStream Transformation

- 무상태 (stateless)
  - 각 배치의 처리가 앞쪽의 배치들에 있는 데이터와 상관없이 진행
  - map(), filter(), reduceByKey()
- 상태 유지 (stateful)
  - 현재 배치의 결과를 만들기 위해 이전 배치의 데이터나 중간 결과를 이용
  - 슬라이딩 윈도우, 시간별 상태 추적

---

#### wordcount (window)

```scala
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.streaming.{Seconds, StreamingContext}

object StreamingExample2 {
  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setMaster("local[*]").setAppName("NetworkWordCount")

    val ssc = new StreamingContext(conf, Seconds(5))
    val sc = ssc.sparkContext
    sc.setLogLevel("WARN")

    val lines = ssc.socketTextStream("localhost", 9999)

    val words = lines.flatMap(_.split(" "))

    val pairs = words.map(word => (word, 1))
    
    // batch 시간 간격 : 5초,  윈도우 시간 간격 : 이전 15초까지
    val wordCounts = pairs.reduceByKeyAndWindow((a:Int,b:Int) => (a + b), Seconds(15), Seconds(5))
    wordCounts.print()

    ssc.start()             // Start the computation
    ssc.awaitTermination()  // Wait for the computation to terminate
  }
}

```

