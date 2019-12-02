#### 단순 매치

```scala
object matchExample {
  def main(args: Array[String]): Unit = {
  
    val bools = Seq(true, false)
    for (bool <- bools){
      bool match {
        case true => println("Got heads")
        case false => println("Got tails")
      }
      
    }
  }
}
```

- if 문으로 구현 가능

- case 절에서 소문자로 시작하는 이름은 뽑아낸 값을 저장할 새로운 변수의 이름으로 간주된다.

  - 앞에서 정의한 값을 참조하고 싶다면 역작은 따옴표로 둘러싸야한다.

  - 대문자로 시작하는 이름은 타입 이름으로 간주

    ```scala
    def checkY(y:Int)={
        for{
          x<-Seq(99,100,101)
        }{
          val str = x match {
            case `y` => "found y!"
            case i:Int => "int: " + i
          }
          println(str)
        }
      }
    ```

    

