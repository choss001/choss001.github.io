# Gradle dependency api vs implementation

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

<p align="center">
  <img src="/images/gradle_dependency_configuration/gradle_clashpath_uml.png" alt="book" width="800"/>
</p>


그래들을 사용하고 있다면 다음과 같은 task로 클래스 패스를 확인 할 수 있습니다.
```
./gradlew dependencies --configuration <compileClasspath | runtimeClasspath>
```

<p align="center">
  <img src="/images/gradle_dependency_configuration/classpath_inspection_task.png" alt="book" width="800"/>
</p>


### 테스트를 위한 디펜던시 구성  
그래들 자바 플러긴은 **testRuntimeOnly**, **testImplementation**, 그리고 **testCompileOnly** 디펜던시 구성을 제공합니다.  
우리가 이러한 디펜던시 구성을 추가함에 따라 어떻게 테스트 compile 그리고 runtime 클래스패스가 생성이 될지 결정됩니다.  
이러한 테스트 구성은 테스트 구성이 아닌것으로 부터 상속되어지고 무엇보다도 테스트코드는 non-test 코드를 필요로합니다.  
  
  
만약 다른 그래들 프로젝트가 사용할 라이브러를 작성한다면 아마도 **Java Library Plugin**을 사용해서 작성하기를 원할지 모릅니다.  
최상위에는 이미 implementation 구성이 설정되어있는 이 플러긴은 추가적인 구성을 추가합니다.   

그래들 프로젝트가 사용하는 자바 라이브러리 플러그가 내장된 프로젝트는 다음 사항이 적용됩니다.







  



<p align="center">
  <img src="/images/gradle_dependency_configuration/gradle_clashpath_test_uml.png" alt="book" width="800"/>
</p>



출처 : [https://tomgregory.com/gradle-implementation-vs-compile-dependencies/](https://tomgregory.com/gradle-implementation-vs-compile-dependencies/)  
[https://docs.gradle.org/current/userguide/java_plugin.html](https://docs.gradle.org/current/userguide/java_plugin.html)  
[https://www.tutorialspoint.com/gradle/gradle_plugins.htm](https://www.tutorialspoint.com/gradle/gradle_plugins.htm)  
[https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa](https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa)  
[https://docs.gradle.org/current/userguide/plugins.html](https://docs.gradle.org/current/userguide/plugins.html)










