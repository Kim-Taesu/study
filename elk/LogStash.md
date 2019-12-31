## LogStash



#### logstash

- ELK에서 input을 담당
- 다양한 경로에서 자료를 가져와 가공한 후 엘라스틱서치에 올릴 수 있다.



#### logstash의 3개의 파이프라인

- input
- filter
- output



#### Filebeats

- [파일비트 설치 경로](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html)

- 설치 후 /etc/filebeat/filebeat.yml을 수정해야한다.

  - filebeat가 로그들을 수집한뒤 엘라스틱서치에 보내지않고 우선적으로 로그스태쉬에 보내기 때문에 설정해야함

  - ```
    인풋 관련 설정
    ==========Filebeat inputs============
    filebeat.inputs:
    # 인풋 타입을 설정한다.
    - type: log
      # enabled 설정을 '반드시' false => true 설정해야 한다.
      enabled: true
    
      # 인풋으로 넣을 데이터 경로
      # 하나의 파일을 넣어도 되고 메타 문자를 사용해 *.log 등 다양하게 가능하다.
      # 하나의 path를 설정하고 엔터누른 뒤 또 추가가 가능하다.
      paths:
        - /home/hp/elk/filebeat/logstash-tutorial-dataset.log
        
    # filebeat가 바로 엘라스틱서치로 가지 않기 때문에 'Elasticssearch output' 관련 설정들을 주석처리 한다.
    #================================ Outputs =====================================
    #-------------------------- Elasticsearch output ------------------------------
    ...
    
    # filebeat가 인풋을 logstash로 보내야 하기 때문에 아래로 설정
    # 꼭 해당 포트가 뚫려있는지 확인해야 한다.
    #----------------------------- Logstash output --------------------------------
    output.logstash:
      # The Logstash hosts
      hosts: ["localhost:5044"]
    
    ```

- 



#### logstash 구성

- 구성
  - 입력
  - 필터
  - 출력
- 입력(input)
  - path : 엘라스틱서치에 넣을 자료 절대 경로
  - sincedb_path : 사용하지 않게 하기 위해 => "/dev/null" 사용
  - start_position : 새 파일이 발견될 때마다 처음부터 파일을 읽도록 플러그인에 지시
- 필터(filter)
  - dissect로 로그 구문 분석
  - mutate 필터와 convert 옵션으로 타입 변경
- 출력(output)
  - stdout : 데이터를 콘솔에 출력
    - codec => rubydebug : 구조를 보여준다. (단순 디버깅화)
  - 

- 코드 예시

  ```
  input {
   file {
     path => ["{파일 위치}"]
     sincedb_path => "/dev/null"
     start_position => "beginning"
    }
  }
  
  filter {
  }
  
  output {
    stdout {
      codec => rubydebug
    }
  }
  ```

  

#### 로그 구문 분석

- filter의 dissect 사용

  - 추출할 필드의 시퀀스와 이 필드 사이의 구분 문자를 지정한다.
  - 구분 문자를 토대로 로그를 분석
  - 예시

  ```
  filter {
   dissect {
     mapping => {
       "message" => "%{data1} %{data2} ..."
      }
     remove_field => [“message”]
    }
  }
  ```

- filter의 grok 사용

  - 일반 표현식 패턴을 사용하여 필드와 구분 문자를 모두 일치시킨다.

  - grok 패턴

    - WORD : 단일 언어에 일치하는 패턴
    - NUMBER : 양이나 음의 정수 또는 부동 소수점에 일치하는 패턴
    - POSINT : 양의 정수와 일치하는 패턴
    - IP : IPv4 또는 IPv6 주소와 일치하는 패턴
    - NOTSPACE : 스페이스가 아닌 모든 것과 일치하는 패턴
    - SPACE : 모든 수의 연속적 스페이스와 일치하는 패턴
    - DATA : 제한된 양의 모든 종류의 데이터와 일치하는 패턴
    - GREEDYDATA : 모든 남아 있는 데이터와 일치하는 패턴

  - 일반적으로 왼쪽부터 시작하여 GREEDYDATA 패턴을 사용하여 나머지 데이터를 캡처하는 패턴

  - 코드 예시

    ```
    #데이터 예시
    1524206424.034   19395 207.96.0.0 TCP_MISS/304 15363 GET /android-chrome-192x192.gif - DIRECT/10.0.5.120 -
    1524206424.145     106 207.96.0.0 TCP_HIT/200 68247 GET /guide/en/logstash/current/images/logstash.gif - NONE/- image/gif
    
    filter {
     grok {
       match => {
         "message" => "%{NUMBER:timestamp}%{SPACE}%{NUMBER:duration}\s%{IP:client_address}\s%{WORD:cache_result}/%{POSINT:status_code}\s%{NUMBER:bytes}\s%{WORD:request_method}\s%{NOTSPACE:url}\s%{NOTSPACE:user}\s%{WORD:hierarchy_code}/%{NOTSPACE:server}\s%{NOTSPACE:content_type}"
        }
       remove_field => ["message"]
      }
    }
    ```

  - grok debugger로 패턴 분석 확인 가능



#### 필드 타입 변경

- mutate필터와 convert 옵션 사용

- ```
  mutate {
   convert => {
     "{field}" => "{type}"
     "{field}" => "{type}"
     "{field}" => "{type}"
     "{field}" => "{type}"
    }
  }
  ```

- dissect 필터에서는 convert_datatype 지시문을 통해 유형 지정

- grok을 이용해 패턴 필드 이름 바로 뒤에 유형을 지정할 수 있다.

