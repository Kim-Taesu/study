## MongoTemplate vs MongoRepository



#### MongoTemplate

- 복잡한 데이터 관련 query를 효율적으로 작성
  - 세부적으로 query문을 제어할 수 있다.
- database에 의존적이다.
  - mongoTemplate을 이용하여 db Query를 작성하면 다른 db로의 전환이 어렵다.



#### MongoRepository

- `MongoTemplate`보다 사용하기 편하다.
- `MongoTemplate` 보다 세부적인 query를 작성하기 힘들다.