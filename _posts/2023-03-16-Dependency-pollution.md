오늘의 주제는 Dependency Pollution 입니다.   

</br>
</br>
gradle과 maven이 나오기 오래전 과거 JAR 파일을 어떻게 설정해줬을까요?  
저는 maven과 gradle이 나온 후에 개발했기 때문에 잘 모르지만 프로젝트가 필요로 하는 모든 JAR file을 직접 다운로드했다고 합니다.  
직접 필요로 하는 JAR file 뿐만 아니라 우리가 사용하는 dependency의 dependency파일 조차도 다운로드 했다고 합니다.  

</br>
운이 좋게 그러한 날들은 끝이났고 오늘날 메이븐과 그래들과 같은 빌드 툴은 우리의 디펜던시들을 해결해줍니다.  
그 빌드 툴들은 우리가 빌드 스크립트에 작성하는 스코프와 설정 규칙을 따라서 행동합니다.  
</br>
</br>
그러나 이것은 부작용을 발생시킵니다.  우리가 일일히 직접 다운로드 하고 디펜던시를 필요로 하는 디펜던시를 수동으로 해줬을때
우리는 각가의 이러한 디펜던시들이 런타임에 필요한지 컴파일에 필요한지 직접 결정할 수 있었습니다.
그러나 오늘날 우리는 올바른 스코프나 configuration에 신경을 덜씁니다.  
이것은 컴파일 할때 너무나 많은 디펜던시가 작용하게 만듭니다.

</br>
</br>
Dependenvy Pollution은 뭘까요?  

우리가 프로젝트 X를 가지고 있고 X는 라이브러리 A와 B를 의존하고 C는 X의 소비자라고 합시다.


출처 : https://reflectoring.io/gradle-pollution-free-dependencies/#whats-dependency-pollution
