#### Dockerfile

- Docker 상에서 작동시킬 컨테이너의 구성 정보를 기술하기 위한 파일
- `docker build` 명령을 통해 Dockerfile에 기술된 구성 정보를 바탕으로 Docker 이미지를 작성



#### Dockerfile의 기본 구문

- 기본 서식

  - `명령   인수`

- 주요 명령

  - |    명령     |            설명            |
    | :---------: | :------------------------: |
    |    FROM     |     베이스 이미지 지정     |
    |     RUN     |         명령 실행          |
    |     CMD     |     컨테이너 실행 명령     |
    |    LABEL    |         라벨 설정          |
    |   EXPOSE    |       포트 익스포트        |
    |     ENV     |          환경변수          |
    |     ADD     |     파일/디렉토리 추가     |
    |    COPY     |         파일 복사          |
    | ENTRYPOINT  |     컨테이너 실행 명령     |
    |   VOLUME    |        볼륨 마운트         |
    |    USER     |        사용자 지정         |
    |   WORKDIR   |       작업 디렉토리        |
    |     ARG     |    Dockerfile 안의 변수    |
    |   ONBUILD   | 빌드 완료 후 실행되는 명령 |
    | STOPSIGNAL  |   시스템 콜 시그널 설정    |
    | HEALTHCHECK |    컨테이너의 상태 체크    |
    |    SHELL    |        기본 쉘 설정        |

- 주석은 `#`을 사용

#### Dockerfile 작성

- FROM 명령은 필수 항목이다.

  - 베이스 이미지를 지정
  - 이미지 다이제스트를 이용하여 고유한 식별자를 선택 가능

  

#### Dockerfile로부터 Docker 이미지 생성

- `docker build -t [생성할 이미지 이름]:[태그명] [Dockerfile의 위치]`
  - 위 명령어에서 dockerfile의 이름은 반드시 `Dockerfile`이어야 한다.
- 특정 dockerfile 설정
  - `docker build -t [생성할 이미지 이름]:[태그명] -f [dockerfile 위치]

#### 명령 실행 (RUN)

- `RUN [실행하고 싶은 명령]`
- 이미지를 작성하기 위해 실행하는 명령을 기술
- 기술 방법
  - Shell 형식으로 기술
  - Exec 형식으로 기술

#### 데몬 실행 (CMD)

- `CMD [실행하고 싶은 명령]`
- 기술 방법
  - Exec 형식으로 기술
  - Shell 형식으로 기술
  - ENTRYPOINT 명령의 파라미터로 기술

#### 환경변수 설정(ENV)

- `ENV [key] [value]`

#### 작업 디렉토리 지정(WORKDIR)

- `WORKDIR [작업 디렉토리 경로]`
- Dockerfile에 쓰여 있는 다음과 같은 명령을 실행하기 위해 작업용 디렉토리를 지정
  - RUN
  - CMD
  - ENTRYPOINT
  - COPY
  - ADD
- 지정한 디렉토리가 존재하지 않으면 새로 작성한다.

#### 사용자 지정(USER)

- `USER [사용자명 / UID]`
- Dockerfile에 쓰여 있는 다음과 같은 명령을 실행하기 위해 작업용 디렉토리를 지정
  - RUN
  - CMD
  - ENTRYPOINT

#### 포트 설정(EXPOSE)

- 컨테이너의 공개 포트 번호를 지정
- `EXPOSE [포트 번호]`

#### 볼륨 마운트(VOLUME)

- `VOLUME ["/마운트 포인트"]`
- 

