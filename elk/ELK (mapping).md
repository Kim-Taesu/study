## ELK (mapping, search)



#### mapping

- mapping == schema

- 포맷을 맞춰야 한다.

- type을 사용하여 형을 맞춘다.

- ```
  curl -XPUT localhost:9200/classes/class/mappings?pretty -d @classesRating_mapping.json -H 'Content-Type: application/json'
  ```

  

#### search

- ```
  # 기본 명령어
  curl -XGET loaclhost:9200/{index}/{type}/_search?pretty 
  
  # 예제 1
  curl -XGET localhost:9200/basketball/record/_search?pretty
  ```



#### search uri

- ```
  # 기본 명령어
  curl -XGET 'loaclhost:9200/{index}/{type}/_search?q={query 내용}&pretty'
  
  # 예제
  curl -XGET 'localhost:9200/basketball/record/_search?q=points:30&pretty'
  
  # 예제 (Request body)
  curl -XGET localhost:9200/basketball/record/_search?pretty -d '
  {
  	"query":{
  		"term":{"points":30}
  	}
  }' -H 'Content-Type: application/json'
  ```

  