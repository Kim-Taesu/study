## ELK (aggregations)



#### aggregations

- document 안에서 조합을 통해서 결과를 도출

- metric aggregations

  - 산술할 때 사용

- 기본 구조

  ```
  "aggregations" : {
  	"<aggregation_name>" : {
  		"<aggregation_type>" : {
  			<aggregation_body>
  		}
  		[,"meta":{[<meta_data_body>]}]?
  		[,"aggregations":{[<sub_aggregation>]+}]?
  	}
  	[,"<aggregation_name_2>" : {...}]*
  }
  ```

- 실행 명령

  - ```
    curl -XGET loaclhost:9200/_search?pretty --data-binary @{aggregation.json} -H 'Content-Type: application/json'
    ```



#### stats

- 최소값, 평균값, 최댓값, 합계를 한번에 출력

- 파일 내용 구성

- size:0 == 우리가 보고싶은 내용만 본다.

- ```
  {
          "size" : 0,
          "aggs" : {
                  "stats_score" : {
                          "stats" : {
                                  "field" : "{value}"
                          }
                  }
          }
  }
  ```

  

  

#### bucket aggregations

- group by랑 같다.
- 