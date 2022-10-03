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

<p align="center">
  <img src="/images/gradle_dependency_configuration/gradle_classpath_test_uml.png" alt="book" width="800"/>
</p>  

### 지금까지의 내용 빠른 요약

**Rule 1** : complie보다는 implementation을 쓰십시오 compile은 Gradle 7+에서 삭제됐습니다.  

**Rule 2** : 만약 compile과 runtime 클래스 패스가 둘다 필요하다면 implementation을 쓰고 아니면 compileOnly 나 runtimeOnly를 고려해 보십시오.  


이제 주제의 본론인 Api vs Implementation에 대해서 설명하려고 합니다.

일단 라이브러리 모듈이 다음과 같이 있다고 가정합니다.
- LibraryA
- LibraryB
- LibraryC
- LibraryD

디펜던시 트리는 다음과 같이 생겼습니다.  

<p align="center">
  <img src="/images/gradle_dependency_configuration/library_modules_tree.png" alt="book" width="800"/>
</p>  

LibraryD :  
```
class ClassD {

    fun tellMeAJoke():String{
        return "You are funny :D"
    }
}
```  

LibraryC :  
```
class ClassC {
    fun tellMeAJoke(): String {
        return "You are funny :C"
    }
}
```  

LibraryB :  
```
class ClassB {

    val b = ClassD()

    fun whereIsMyJoke(): String {
        return b.tellMeAJoke()
    }
}
```  

LibraryA :  
```
class ClassA {

    val c = ClassC()

    fun whereIsMyJoke(): String {
        return c.tellMeAJoke()
    }
}
```  

위에 그림을 보면 **LibraryA** 와 **LibraryB**는 각각 **LibraryC** 와 **LibraryD**를 의존합니다.
그러므로 build.gradle 파일에 디펜던시를 추가해야됩니다.

### Compile or Api  
새로운 api 키워드는 정확히 옛날의 compile 키워드랑 같습니다.  
그러므로 만약 모든 compile가 api로 전부 교체된다고 하더라도 잘 작동될것입니다.  
이제 LibraryD와 LibraryB를 api키워드를 사용해서 APP Module build.gradle 파일에 추가해봅시다.

```
dependencies {
          . . . . 
          api project(path: ':libraryD')
}
```
비슷하게 LibraryB도 앱 모듈에 추가해봅시다.

```
dependencies {
          . . . . 
          api project(path: ':libraryB')
}
```
이제 LibraryB와 LibraryD는 앱 모듈에서 다음 예와 같이 접근 가능합니다.
```
fun getJokeFromB(): String {
  return ClassB().whereIsMyJoke()
}

fun getJokeFromD(): String {
  return ClassD().tellMeAJoke()
}
```

### Implementation  
이제부터는 implemetation과 api의 다른점을 찾아볼 시간입니다.  
implementation 키워드를 이용해서 LibraryC와 LibraryA를 추가해봅시다.  
```
dependencies {
          . . . . 
          implementation project(path: ':libraryC')
}
```
앱 모듈에서 다음과 같이 똑같이 적용합니다.

```
dependencies {
          . . . . 
          implementation project(path: ':libraryA')
}
```

이제 만약 앱 모듈에서부터 LibraryC 를 접근하려고 하면 다음과 같이 에러를 던질것입니다.

<p align="center">
  <img src="/images/gradle_dependency_configuration/LibraryC_can't_access.png" alt="book" width="800"/>
</p>  
  
만약 api 대신에 implementation을 쓴다면 앱 모듈에서부터 바로 LibraryC를 접근할수 없다는 뜻을 의미합니다.  
그렇다면 이것의 이점은 무엇일까요??


#### Implementation vs api  
첫번째 시나리오는 다음과 같습니다.  
LibraryD는 api로 컴파일되고 LibraryD의 구현체가 바뀐다면 그래들은 LibraryD와 LibraryB를 모두 리컴파일하고 LibraryB를 사용하는 다른 모든 모듈은 LibraryD를 사용할수 있습니다.
<p align="center">
  <img src="/images/gradle_dependency_configuration/first_scenario.png" alt="book" width="800"/>
</p>  



하지만 두번째 시나리오에서는  
만약 LibraryC가 변한다고 해도 그래들은 단지 LibraryC만 리컴파일합니다. 따라서 다른 모듈은 LibraryC를 바로 사용할수 없게 됩니다.  
<p align="center">
  <img src="/images/gradle_dependency_configuration/second_scenario.png" alt="book" width="800"/>
</p>  


만약 우리가 엄청나게 많은 모듈을 갖고있다면 이 전략은 빌드 속도를 현저히 증가 시킬수 있습니다.  



#### 요약  
모든 **compile**을 전부 **implementation**으로 바꿔서 빌드를 합니다.  만약 빌드가 성공적으로 된다면 아주 잘된것이고  
만약 dependency가 부족해보인다면 그때 그러한 라이브러리들을 **api** 키워드로 바꾸면 됩니다.














  







출처 : [https://tomgregory.com/gradle-implementation-vs-compile-dependencies/](https://tomgregory.com/gradle-implementation-vs-compile-dependencies/)  
[https://docs.gradle.org/current/userguide/java_plugin.html](https://docs.gradle.org/current/userguide/java_plugin.html)  
[https://www.tutorialspoint.com/gradle/gradle_plugins.htm](https://www.tutorialspoint.com/gradle/gradle_plugins.htm)  
[https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa](https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa)  
[https://docs.gradle.org/current/userguide/plugins.html](https://docs.gradle.org/current/userguide/plugins.html)










