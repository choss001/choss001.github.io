# JVM의 자바 메모리 관리자   
  
  
자바 어플리케이션을 다루기 위해서는 Java memory management를 알아야합니다. 기본적으로 이것은 새로운 오브젝트를 할당하거나 안쓰는 오브젝트를 지우는 과정입니다.  
  
이 글에서는 Java Virtual Machine(JVM)의 메모리 관리 이해, 메모리 모니터링, 메모리 모니터링 사용법 그리고 Garbage Collection(Gc)의 행동에 대해 알아볼 생각입니다.  

    
<br />
<br />
<br />
<br />
    
    
    
      
  
***Java Virtual Machine (JVM)***  
  
JVM은 컴퓨터를 Java program을 실행 가능하게 만드는 추상적인 컴퓨팅 머신입니다. 여기에는 3개의 개념이 있습니다.  
- 스펙(specification) : JVM 실행을 지정하는 곳 구현은 SUN이나 다른 회사에서 제공합니다.
- 구현(implementation) : JRE으로 알려져 있습니다.
- 인스턴스(instance) : 자바 클래스를 실행하기 위한 커맨트를 작성후 JVM의 인스턴스가 실행됩니다.
   
     
JVM은 코드를 불러오고, 코드를 검증하고 코드를 실행하고, 메모리를 관리하고 (Operation System(OS)에서 메모리를 할당하는것도 포함합니다.), Java allocation 관리합니다.   
그리고 최정적으로 런타임 환경을 제공합니다.  
  
  
Java(JVM) Memory Structure
JVM 메모리는 여러 부분으로 나뉘어져 있습니다. Heap Memory, Non-Heap Memory 그리고 나머지  
<p align="center">
 <img src="/images/JVM/JVM-Memory-Model.jpg" alt="book" width="500"/>
</p>  
<br />
<br />
**Heap Memory**  

  
힙 메모리는 모든 자바 클래스 인스턴스들과 어레이가 할당된 런타임 데이터 영역입니다. JVM이 시작할때 힙 메모리는 생성되고 어플리케이션이 작동할때 그 사이즈를 줄어들 수도 늘어날 수도 있습니다.  
힙의 사이즈는 -Xms Vm 옵션을 사용해서 지정할 수도 있습니다. 힙메모리는 고정된 사이즈가 될 수도 있고 가비지 컬렉션 전략에 따라 변할 수도 있습니다. 힙의 최대 사이즈는 -Xmx option을 이용해서 지정할 수 있습니다.  
기본적으로 힙의 최대 사이즈는 64MB로 지정되어 있습니다.  
<br />
<br />
<br />

**Non-Heap Memory**  
JVM은 heap 메모리 말고도 Non-Heap Memory라고 불리는 메모리가 있습니다. JVM이 시작될때 Non-Heap Memory는 생성되고 클래스의 구조 예를들어 런타임 정적 pool, field and method data, 메소드를 위한 코드 그리고 생성자 등 또한 interned String을 저장합니다.  
* intered String  
  스트링을 스트링 풀의 더하는 과정을 의미함 스트링이 interend 됐다는건 같은 메모리에 할당된것을 의미함
``` 
String s1 = "interned";           // This string is interned automatically.
String s2 = new String("interned").intern();  // Explicitly intern the string.

System.out.println(s1 == s2);     // This will print true, as both strings point to the same interned string.
```
<br />
기본적으로 맥시멈 non-heap 메모리는 64MB이지만 -XX:MaxPermSize VM 옵션을 통해서 수정할 수 있습니다.  
<br />
<br /><br />
**Other Memory**  

  JVM은 이 영역을 JVM 코드 자신, JVM 내부 구조, 불러온 프로파일러 에이전트 코드, 그리고 데이터 등등을 저장하는데 사용합니다. 


  
  

https://www.betsol.com/blog/java-memory-management-for-java-virtual-machine-jvm/






