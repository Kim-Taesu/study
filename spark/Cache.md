#### 캐싱

- `cache`하는 작업은 lazy한 작업이므로 `cahce`를 할 데이터가 `action` 함수를 실행시키지 않으면 cache 되지 않는다.
- 셔플된 데이터는 자동적으로 `caching`된다.
  - OOM 유발할 수도..



#### 중간 결과를 캐쉬하는 경우

- `action`이 발생되기 전에 cache가 필요할 때

- `df.checkpoint()`로 중간 결과를 HDFS에 저장한다.

  - execution time이 주요 요인이 아닐경우

- action trigger 설정

  - `count()`, `show()`, ...
  - `show()` 메소드는 전체 데이터프레임을 cache 안할수도 있다.

- `cache`할 dataframe을 이용하여 새로운 dataframe을 생성

  ```scala
  def isCached(df: DataFrame) = spark.sharedState.cacheManager.lookupCachedData(df.queryExecution.logical).isDefined
  val df = sparkSession.read.json(…)
  val tempDF = df.withColumn(“number”, lit(1)).cache()
  println(isCached(tempDF)) // false
  val newDF = session.createDataFrame(df.rdd, df.schema).cache()
  println(isCached(newDF)) // true
  ```

  