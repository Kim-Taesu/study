## ELK (bulk)



#### 여러 개의 document를 한번에 삽입

- ```
  curl -XPOST localhost:9200/_bulk?pretty --data-binary @classes.json -H 'Content-Type: application/json'
  ```

- bulk file

  - 2개의 라인으로 구성
    - meta information
      - _index, _type, _id 지정
    - data

