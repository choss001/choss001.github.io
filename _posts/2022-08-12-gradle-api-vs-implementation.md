# api vs implementation

오늘은 평소 헷갈렸던 gradle에서 **api** dependency configuration과 **implementation** dependency configuration에 대해서 알아보려고 합니다.  

평소 Gradle의 대한 공부가 필요하다고 느꼈지만 제 공부 우선순위는 항상 다른것들이 상위에 위치해 있어서 Gradle의 대한 공부는 뒤로 밀렸었는데요
이번 기회에 조금 공부하고 포스트 하려고합니다.  


### 디펜던시 선언 방법

gradle에서 자바 dependencies를 선언할때 우리는 다음과 같이 dependency 설정을 할당합니다.

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:2.2.5.RELEASE'
}
```

위 코드에서는 **implementation** 설정을 사용했지만 Gradle 6 버전 이하에서는 그냥 **compile** 설정을 사용할 수도 있습니다.  
```
dependencies {
    compile 'org.springframework.boot:spring-boot-starter-web:2.2.5.RELEASE'
}
```
하지만 **compile**은 Deprecated되었고 컴파일 단계 디펜던시는 **implementation**을 사용하도록 바뀌었습니다.  


**compile** 설정은 Gradle 7.0에서 삭제되었습니다. **implementation** 디펜던시 설정이 **compile** 디펜던시 설정과 같은 기능을 제공합니다.  

즉 항상 **compile** 대신 **implementation**을 사용하면 됩니다. 

### implementation dependency는 무엇인가?

자바를 building할때와 running할때 두개의 classpaths가 있어야됩니다.

1.**Compile classpath** - JDK가 java code를 .class files로 컴파일 할때 필요로 하는 의존 리스트입니다.  

2.**Runtime classpath** - 컴파일된 자바 코드가 실제로 실행할때 필요로 하는 의존 리스트입니다.  

그래들에서 디펜던시 설정을 할때 사실 실제로 우리가 하는일은 어느 클래스패스에서 보여지게 할것인지 입니다.  

1. **complieOnly** - 컴파일 패스에만 설정
2. **runtimeOnly** - 런타임 패스에만 설정
3. **implementation** - 위 두개의 패스에 둘다 설정  


런타임, 컴파일 패스 둘다 설정하려면 **implementation**을 사용하면 됩니다.  
하지만 만약 아니라면 **compileOnly**나 **runtimeOnly**를 고려해보시길 바랍니다.  

그렇다면 왜 특정한 클래스패스 설정에 대해 이렇게 신경써야 할까요. 그 이유는 약간의 이익 때문입니다.

  - **빠른 컴파일속도** 클래스패스에 적은 디펜던시가 있기 때문입니다.
  - **코드를 작성할때 실수로 원치않은 클래스 사용을 하지 않는다**  디펜던시 설정을 런타임 클래스패스에만 설정한다면
  코드 작성할때 실수로 사용하는 것을 방지
  -  **클래스 패스 정리** 복잡함 감소

```

```










