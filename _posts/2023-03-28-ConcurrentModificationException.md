# ConcurrentModificationException을 피하는 방법

컬렉션으로 작업하다보면 ConcurrentModificationExeption은 일반적으로 잘 발생하는 에러입니다.  
iterated될때 해당작업이 수정된다면 oncurrentModificationException은 fail-fast로 사용됩니다.

여기서 fail-fast는 collection이 만약 쓰레
</br>
</br>
</br>

이 익셉션은 허락없이 동시에 수정이 시도될때 발생합니다.  
예를들어 쓰레드가 Iterator를 사용하면서 엘리먼트를 탐색하고있을때 Collection이 수정된다면 ConcurrentModificationException은 Iterator.next()메소드로부터 발생됩니다.

ConcurrentModificationException은 멀티쓰레드 환경에서도 싱글쓰레드 환경에서도 발생할 수 있습니다.


출처 : https://rollbar.com/blog/java-concurrentmodificationexception/ 
</br>
