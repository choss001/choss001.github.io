# ConcurrentModificationException을 피하는 방법

컬렉션으로 작업하다보면 ConcurrentModificationExeption은 일반적으로 잘 발생하는 에러입니다.  
iterated될때 해당작업이 수정된다면 oncurrentModificationException은 fail-fast로 사용됩니다.
   
## fail-fast   

여기서 fail-fast는 쓰레드가 엘리먼트를 iterator로 탐색하는 도중에 엘리먼트가 수정된다면 즉시 에러를 발생시키는것을 의미합니다.  
더 자세하게 말하자면 자바에 대해 아는것이 중요합니다. 일단 동시 프로세스를 수행하기 위해서는 쓰레드를 사용하는게 일반적입니다.  
쓰레드는 여러 작업을 동시에 수행하기 위해서 분리된 수행할수 있는 main program을 의미합니다.  
쓰레드가 컬렉션을 탐색할때 쓰레드는 컬렉션을 순서대로 읽습니다.  
만약 다른 두번째 쓰레드가 첫번째 쓰레드가 엘리먼트를 탐색하고 있을때 수정을 시도한다면 예기지 못한 행동을 발생시킬 여지가 있습니다.  
이것을 방지하기 위해 컬렉션은 수정이 감지되는 즉시 ConcurrentModificationException을 발생하게 설계되어있습니다.  
이러한 행동은 자료구조의 데이터 오염을 방지하는데 도움을 줍니다.  
요약하자면 fail-fast 데이터 오염을 방지하기 위한 행동입니다.  
   
<br />
   

이 익셉션은 허락없이 동시에 수정이 시도될때 발생합니다.  
예를들어 쓰레드가 Iterator를 사용하면서 엘리먼트를 탐색하고있을때 Collection이 수정된다면 ConcurrentModificationException은 Iterator.next()메소드로부터 발생됩니다.
   
ConcurrentModificationException은 멀티쓰레드 환경에서도 싱글쓰레드 환경에서도 발생할 수 있습니다.
   
멀티쓰레드 환경 - 만약 쓰레드가 Iterator를 사용해서 컬렉션을 탐색하고 있을때 해당 컬렉션을 다른 쓰레드가 엘리먼트를 더하거나 삭제할때  
   
싱글쓰레드 환경 - 향상된 포문 안에서 엘리먼트들이 탐색되고 있을때 ArrayList의 remove() 메소드를 사용할
   
      
## ConcurrentModificationException 예제  
   
향상된 포문을 사용할때 ArrayList의 remove()메소드를 사용해서 엘리먼트를 삭제시도할때 해당 에러가 발생하는 예제가 아래와 같이 있습니다.  
   


```
import java.util.ArrayList;
import java.util.List;

public class ConcurrentModificationExceptionExample {
    public static void main(String args[]) {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");

        for (String elem : list) {
            if (elem.equals("a")) {
                list.remove(elem);
            }
        }
    }
}
```
   
향상된 포문을 내부적으로 Iterator를 사용해서 엘리먼트를 탐색하기때문에 위의 코드는 iterator 대신 Collection의 remove()메소드를 사용하기때문에 ConcurrentModificationException을 발생시킵니다.  
   
```  
Exception in thread "main" java.util.ConcurrentModificationException
    at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1013)
    at java.base/java.util.ArrayList$Itr.next(ArrayList.java:967)
    at ConcurrentModificationExceptionExample.main(ConcurrentModificationExceptionExample.java:12)
```  
   
## ConcurrentModificationException을 피하는 방법  
위의 익셉션은 향상된 포문 대신 전통적인 포문을 사용해서 해결할 수 있습니다. 전통적인 for문은 Collection의 Iterator를 사용하지 않기 때문에 ConcurrentModificationException을 발생시키지 않습니다.  
<br />
<br />
```  
import java.util.ArrayList;
import java.util.List;

public class ConcurrentModificationExceptionExample {
    public static void main(String args[]) {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");

        for (int i = 0; i < list.size(); i++) {
            if (list.get(i).equals("a")) {
                list.remove(list.get(i));
            }
        }

        System.out.println(list);
    }
}
```  
ConcurrentModificationException은 Iterator에 속해있고 ArrayList의remove()메소드에 속해있지 않으므로 위으 코드는 다음과 같은 결과가 나옵니다.  
<br />
```  
[b, c, d]
```   
위의 익셉션은 또한 Iterator.remove()메소드를 사용한다면 해결될 수 있습니다.   
java8에서 소개된 해당 조건이 성립한다면 엘리먼트를 지우는 Collection.removeIf()메소드를 사용해서도 해결할 수 있습니다.  

## 멀티쓰레드 환경에서 ConcurrentModificationException을 피하는 방법   

멀티쓰레드 환경에서 해당 익셉션을 피하기 위해서는 특정한 준비가 필요합니다.  

- collection 대신 array를 사용해서 탐색하기 - 이 방법은 스몰 사이즈 리스트에서는 잘 작동하지만 큰 사이즈에서는 퍼포먼스가 잘 나오지 않습니다.
- `synchronized` block을 사용해서 컬렉션은 잠가버리기 - 이 방법은 다양한 목적의 멀티쓰레드 환경에서 가장 효과적이지 않은 방법일 수 있습니다.   
- ConcurrentHashMap, CopyOnWriteArrayList와 같은 concurrent collection클래스를 사용하면 익셉션을 피하는데 도움이 될 수 있습니다.   


<br />
<br />


출처 : https://rollbar.com/blog/java-concurrentmodificationexception/ 
<br />
