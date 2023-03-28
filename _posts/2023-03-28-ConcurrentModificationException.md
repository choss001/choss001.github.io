# ConcurrentModificationException을 피하는 방법

컬렉션으로 작업하다보면 ConcurrentModificationExeption은 일반적으로 잘 발생하는 에러입니다.  
iterated될때 해당작업이 수정된다면 oncurrentModificationException은 fail-fast로 사용됩니다.

</br>
</br>

이 익셉션은 허락없이 동시에 수정이 시도될때 발생합니다.  
예를들어 쓰레드가 Iterator를 사용하면서 엘리먼트를 탐색하고있을때 Collection이 수정된다면 ConcurrentException은 


출처 : https://rollbar.com/blog/java-concurrentmodificationexception/ 
</br>
