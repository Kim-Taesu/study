## ELK (get, post, put, delete)

-H 'Content-Type: application/json'

#### elk

- keyword 중심
- RDB 보다 search 관점에서는 매우 빠르다.



| Elastic Search |   Relational DB    |
| :------------: | :----------------: |
|     Index      |      Database      |
|      Type      |       Table        |
|    Document    |        Row         |
|     Field      |       Column       |
|    Mapping     |       Schema       |
|      GET       |       Select       |
|      PUT       |       Update       |
|      POST      |       Insert       |
|     DELETE     |       Delete       |
|      _id       |    Primary key     |
|    Analyze     |       Index        |
|     Shard      | Physical partition |
|     Route      | Logical partition  |
|   Query DSL    |        SQL         |
|                |                    |

```python
# select * from class where id = 1
curl -XGET localhost:9200/classes/class/1

# insert into class values (xxx)
curl -XPOST localhost:9200/classes/class/1 -d '{xxx}'

# update class set xxx where id = 1;
curl -XPUT localhost:9200/classes/class/1 -d '{xxx}'

# delete from class where id = 1
curl -XDELETE localhost:9200/classes/class/1
```



#### pretty

- curl 명령 결과값을 깔끔하게 출력

- ```
  curl -XGET localhost:9200/classes?pretty
  ```

  



#### Index 생성

- ```
  # classes 라는 index 생성
  curl -XPUT localhost:9200/classes
  ```



#### index 삭제

- ```
  # classes index 삭제
  curl -XDELETE localhost:9200/classes
  ```



#### document 생성

- -d 옵션 : json 포맷으로 전달

- index가 없을 때도 가능하다. 

  - index명과 type 명을 기입

  - ```
    curl -XPOST localhost:9200/{index 명}/{type 명}/{id}/ -d
    ```

- ```
  curl -XPOST localhost:9200/classes/class/1/ -d '
  {"title":"Algorithm","professor":"John"}'
  ```

- 아래와 같은 에러 발생 시 (content-type을 인한 오류)

  ```
  {
    "error" : "Content-Type header [application/x-www-form-urlencoded] is not supported",
    "status" : 406
  }
  ```

  - 명령어 옵션에 아래 코드를 추가한 뒤 명령어 입력

  - ```
    -H 'Content-Type: application/json'
    
    # total code
    curl -XPOST localhost:9200/classes/class/1/ -d '
    {"title":"Algorithm","professor":"John"}' -H 'Content-Type: application/json'
    ```



#### 파일에서의 document 생성

- ```
  curl -XPOST localhost:9200/{index 명}/{type 명}/{id}/ -d @{filename.json}
  ```

  