#### 스파크 데이터 타입으로 변환

- 데이터 타입 변환은 lit 함수를 사용
- lit 함수는 다른 언어의 데이터 타입을 스파크 데이터 타입에 맞게 변환

#### 스칼라 에서의 동등 비교

- 일치 : ===
  - equalTo 메소드 사용 가능
- 불일치 : =!=
  - not 메소드 사용 가능

#### 불리언 데이터 타입

- 불리언 식에는 일치 조건 뿐만 아니라 작다, 크다와 같은 비교 연산 조건을 사용할 수 있다.
- and 메서드나 or 메서드를 사용해서 불리언 표현식을 여러 부분에 지정할 수 있습니다.
- 불리언 표현식을 사용하는 경우 항상 모든 표현식을 and 메서드로 묶어 차례대로 필터를 적용해야 한다.
  - 불리언 문을 차례대로 표현하더라도 스파크는 내부적으로 and 구문을 필터 사이에 추가해 모든 필터를 하나의 문자응로 변환

#### 문자열 데이터 타입 다루기

| 함수    | 기능                                                         |
| ------- | ------------------------------------------------------------ |
| initcap | 주어진 문자열에서 공백으로 나뉘는 모든 단어의 첫 글자를 대문자로 변경 |
| lower   | 문자열 전체를 소문자로 변경                                  |
| upper   | 문자열 전체를 대문자로 변경                                  |
| trim    | 문자열의 좌측, 우측 공백 제거                                |



#### 사용자 정의 함수(UDF)

- 파이썬이나 스칼라 그리고 외부 라이브러리를 사용해 사용자가 원하는 형태로 트랜스포메이션을 만들 수 있게 한다.
- UDF는 하나 이상의 컬럼을 입력으로 받고 반환할 수 있다.
- 레코드별로 데이터를 처리하는 함수
- Spark에서의  UDF 실행
  - UDF 함수 만들고 테스트
  - 스파크에 등록하기 위해 드라이버에서 함수를 직렬화하고 네트워크를 통해 모든 익스큐터 프로세스로 전달
    - 스칼라나 자바로 함수를 작성했다면 JVM 환경에서만 사용 가능

---



#### 집계 연산

- 집계 : 무언가를 함께 모으는 행위이며 빅데이터 분석의 초석
- 집계를 수행하려면 키나 그룹을 지정하고
  - 하나 이상의 컬럼을 변환하는 방법을 지정하는 집계 함수를 사용
- 여러 입력값이 주어지면 그룹별로 결과를 생성

#### 그룹화 데이터 타입 생성

- select 구문에서 집계를 수행하여 DataFrame의 전체 데이터를 요약
- `group by`는 하나 이상의 키를 지정할 수 있으며 값을 가진 컬럼을 변환하기 위해 다른 집계 함수를 사용할 수 있다.
- `윈도우(window)`는 하나 이상의 키를 지정할 수 있으며 값을 가진 컬럼을 변환하기 위해 다른 집계 함수를 사용
- `그룹화 셋(grouping set)`은 서로 다른 레벨의 값을 집계할 때 사용
  - SQL, DataFrame의 롤업 그리고 큐브를 사용할 수 있다.
- `롤업(rollup)`은 하나 이상의 키를 지정할 수 있다.
  - 컬럼을 변환하는 데 다른 집계 함수를 사용하여 계층적으로 요약된 값을 구할 수 있다.
- `큐브(cubd)`는 하나 이상의 키를 지정할 수 있으며 값을 가진 컬럼을 변환하기 위해 다른 집계 함수를 사용
  - 큐브는 모든 컬럼 조합에 대한 요약 값을 계산

#### 지정된 집계 함수에 따라 그룹화된 결과는 `RelationalGroupedDataset`을 반환

#### 그룹화

- 하나 이상의 컬럼을 그룹화하고 집계 연산을 수행하는 두 단계로 이루어짐
  - 첫 번째 단계에서 `RelationalGroupedDataset`이 반환
  - 두 번째 단계에서 `DataFrame`이 반환
- 그룹의 기준이 되는 컬럼을 여러 개 지정할 수 있다.

#### 윈도우 함수

- 데이터의 특정 `윈도우`를 대상으로 고유의 집계 연산을 수행
- 데이터의 `윈도우`는 현재 데이터에 대한 참조를 사용해 정의
- 윈도우 명세는 함수에 전달될 로우를 결정
- 윈도우 함수는 프레임에 입력되는 모든 로우에 대해 결과값을 계산
  - `group-by` 함수를 사용하면 모든 로우 레코드가 단일 그룹으로만 이동
  - 가장 흔히 사용되는 `롤링 평균`
- 윈도우 함수
  - 랭크 함수
  - 분석 함수
  - 집계 함수

#### 윈도우 함수 정의

- 윈도우 명세를 만든다.
  - partitionBy : 그룹을 어떻게 나눌지 결정
  - orderBy : 파티션의 정렬 방ㅅ기을 정의
  - rowsBetween : 입력된 로우의 참조를 기반으로 프레임에 로우가 포함될 수 있는지를 결정

---

#### SparkSession

- ```scala
  import org.apache.spark.sql.SparkSession
  
  val spark = SparkSession
    .builder()
    .appName("Spark SQL basic example")
    .config("spark.some.config.option", "some-value")
    .getOrCreate()
  
  // For implicit conversions like converting RDDs to DataFrames
  import spark.implicits._
  ```



#### implicits Object

- Scala object 또는 RDD들을 `Dataset`, `Dataframe`, `Columns` 등으로 암시적 변환
  - 변환하기 전에 `import SparkSession.implicits._`를 선언해줘야 한다.
- `SparkSession` 객체에 정의되어 있다.



```scala
val checkList = ListBuffer.fill(N)(SOME_VALUE)
var flag = true
val baseDataframe = spark.read.csv.{...}
val threshold = THRESHOLD_VALUE

while(flag){
    // dataframe operation
	val someDataframe1 = checkList.toDF("SOME_COL")
    val someDataframe2 = baseDataframe.join(someDataframe1)
    val someDataframe3 = someDataframe2.groupBy(...).sum(...)
    val someDataframe4 = someDataframe2.join(someDataframe3)
    val someDataframe5 = someDataframe4.groupBy(...).sum(...)
    
    // update checkList
    val checkValue = someDataframe5.agg(max("SOME_COL")).first.getDouble(0)
    
    if(checkValue < threshold){
        flag=false
    }
    else{
        checkList.clear()
        someDataframe5.collect().foreach(row=>{checkList.append(row.getDouble(0))})
    }
}
```

