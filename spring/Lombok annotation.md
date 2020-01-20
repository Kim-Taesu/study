## Lombok annotation



#### 접근자 / 설정자 

- `@Getter` : 필드 값에 선언하면 자동으로 getXxx()를 생성해준다.
- `@Setter` : 필드 값에 선언하면 자동으로 setXxx()를 생성해준다.
- **클래스 레벨에 선언하면 모든 필드 값에 적용된다.**



#### 생성자

- `@NoArgsConstructor` : 파라미터가 없는 기본 생성자 생성
- `@AllArgsConstructor` : 모든 필드 값을 파라미터로 받는 생성자를 생성
- `@RequiredArgsConstructor` : `final`이나 `@NonNull` 인 필드 값만 파라미터로 받는 생성자 생성



#### toString

- `@ToString` : 해당 필드 값을 출력해주는 toString() 메소드 자동 생성
  - `exclude` 옵션을 사용하여, 특정 필드를 제외할 수 있다.
- 



#### Builder

- `@Builder` : 해당 클래스에 빌더를 자동으로 추가



#### Null 관련

- `@NonNull` : 자동으로 null 체크를 해준다.
  - null 값이 넘어오면 `NullPointerException` 예외 발생
  - 

`@EqualsAndHashCode` : 해당 객체의 equals()와 hashCode() 메소드를 생성합니다.



`@Data` : `@ToString`, `@EqualsAndHashCode`, `@Getter`, `@Setter`, `@RequiredArgsConstructor`를 합쳐 둔 어노테이션



`@Cleanup` : 자동으로 close() 메소드를 호출