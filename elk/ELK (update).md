## ELK (update)

#### 

#### 업데이트

- ```
  curl -XPOST localhost:9200/{index}/{type}/{id}_update -d'
  {...}' -H 'Content-Type: application/json'
  ```



#### 스크립트를 사용한 업데이트

- ```
  curl -XPOST localhost:9200/{index}/{type}/{id}/_update -d'
  {"script":"{script 내용}"}' -H 'Content-Type: application/json'
  
  # 예제
  curl -XPOST localhost:9200/classes/class/1/_update?pretty -d'
  {"script":"ctx._source.unit+=5"}' -H 'Content-Type: application/json'
  ```

  

