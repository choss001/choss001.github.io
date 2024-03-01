gradle과 maven이 나오기 오래전 과거 JAR 파일을 어떻게 설정해줬을까요?  
저는 maven과 gradle이 나온 후에 개발했기 때문에 잘 모르지만 프로젝트가 필요로 하는 모든 JAR file을 직접 다운로드했다고 합니다.  
직접 필요로 하는 JAR file 뿐만 아니라 우리가 사용하는 dependency의 dependency파일 일일이 손으로 다운로드 했다고 합니다.  
  
<br/>  
운이 좋게 그렇게 힘들게 손으로 관리하는 날은 끝났고 오늘날 메이븐과 그래들과 같은 빌드 툴은 우리의 디펜던시들을 해결해줍니다.   
그 빌드 툴들은 우리가 빌드 스크립트에 작성하는 스코프와 설정 규칙을 따라서 행동합니다.  
<br/>  
<br/>  
그러나 여기에는 부작용이 있는데요.  우리가 일일히 직접 다운로드 하고 디펜던시를 필요로 하는 디펜던시를 수동으로 해줬을때
우리는 각가의 이러한 디펜던시들이 런타임에 필요한지 컴파일에 필요한지 직접 결정할 수 있었습니다.  
오늘날 우리는 올바른 스코프나 configuration에 신경을 덜씁니다.  
이것은 컴파일 할때 너무나 많은 불필요한 디펜던시가 작용하게 만듭니다.  
  
<br/>  
<br/>  

## Dependenvy Pollution은 뭘까요?  

<br/>  
우리가 프로젝트 X를 가지고 있고 X는 라이브러리 A와 B를 의존하고 C는 X의 소비자라고 합시다.  

<p align="center">
  <img src="/images/dependency_pollution/implicit-dependency_hu179702c5f5a846ec7a9c6c32bf3fa227_84305_700x0_resize_q90_box.jpg" alt="book" width="800"/>
</p>

<br/>  
<br/>  

C는 A와 B에 대한 간접적인 의존성을 가지고 있습니다. 왜냐면 X가 작동하기 위해서는 A와 B가 필요하기 때문이죠.

다음 두 경우가 있습니다.

- X는 A와 B의 클래스들을 X의 코드 안에 사용 할 수 있고
- C는 X,A 그리고 B의 클래스들을 C의 코드 안에 사용 할 수 있습니다.

X의 디펜던시가 C의 컴파일 타임에 누출됩니다. 이 경우를 "dependency pollution"이라고 부릅니다.

## Dependency pollution의 문제
소비자의 컴파일 시간을 오염시키는 것에 대해 이야기 해보겠습니다.

## Accidental Dependencies

첫번째 문제는 컴파일타임 디펜던시 사고가 쉽게 발생 할 수 있다는 것입니다.  
<br/>  
예를들어 C의 개발자가 A라이브러리의 몇몇의 클래스들을 C의 코드에 사용한다고 생각해보세요. 그는 아마도 A는 실제로는 X의 디펜던시라는 것을 알지 못할 확률이 크고 A가 C의 디펜던시가 아니라는 사실을 알기 어려울 것입니다. 그리고  IDE는 기쁘게 그에게 이러한 클래스들을 클래스 패스에 제공할 것입니다.  
<br/>  
이제 X의 개발자가 다음 X의 버전에서 더이상 A의 라이브러리가 필요하지 않게 되었습니다. 그래서 그들은 A를 제거한 완벽히 전버전이 호환 가능한 마이너 업데이트를 진행했습니다. X의 개발자들은 X의 API를 전혀 수정하지 않았습니다.  
<br/>  
<p align="center">
  <img src="/images/dependency_pollution/implicit-dependency_hu179702c5f5a846ec7a9c6c32bf3fa227_84305_700x0_resize_q91_box.jpg" alt="book" width="800"/>
</p>

그러나 C의 개발자가 X의 다음 버전을 업데이트 하는 순간 그들은 A의 라이브러리가 없기 때문에 컴파일 에러를 발견할 것입니다 심지어 한줄의 코드도 변경하지 않았고 X가 완벽히 하위의 버전을 지원 함에도 불구하고 말이죠.  
<br/>  
만약 여기서 우리가 Compile-time dependency를 고객에게 전파한다면 고객은 원치 않을지라도 실수로 Complie-time dependency를 만들 수 있습니다. 그리고 고객은 결국 다른 프로젝트의 디펜던시가 변경 되었을때 소스를 변경해야 할것입니다.
<br/>  
이건 그들의 코드가 통제를 잃는 것입니다.
<br/>  

## Unnecessary Recompiles  
<br/>  
A,B,C 그리고 X가 우리 소유의 모듈이라고 생각해보세요.  
매번 A,b의 코드가 바뀔때마다 C는 다시 컴파일 해야됩니다. 심지어 모듈 C는 A와 B의 코드를 사용하지 않더라도 말입니다.  
<br/>  
C가 X를 통해서 A,B의 대한 compile-time dependency를 갖고 있기 때문에 계속 반복될 것입니다.  
빌드툴은 기쁘게(당연히) 모든 모듈이 변경될 때마다 전부 다시 컴파일 할 것입니다.  
<br/>  
이건 아마 프로젝트 안에 있는 모듈이 보다 정적이라면 문제가 되지 않을 것입니다. 하지만 만약 그보다 자주 변경된다면 이 것은 불필요한 긴 빌드 타임을 만들 것 입니다.  
<br/>  
## Unnecessary Reasons to Change
<br/>  
위에서 언급된 문제는 결국 오직 한 모듈이 변경될 이유는 하나뿐이어야 된다는 Single Reponsibility Principle(SRP)에 위배됩니다.  
<br/>  
결국 우리는 C의 요구사항이 변하지 않았음에도 C를 수정해야 할 것 입니다. A와 B가 그들의 코드를 수정할때 우리는 딱 맞게 다시 수정해야됩니다.  
<br/>  
만약 모듈이 변해야 될 이유가 하나였다면 우리는 우리의 코드이 통제권을 가질수 있고 X를 통한 compile-time dependency를 가지고 있다면 우리는 코드 통제권을 잃을 것입니다.  
<br/>  

# Gradle’s Solution
<br/>  
이러한 무분별한 compile-time dependency들을 막기 위한 오늘날 빌드 툴들의 능력은 무엇일까요?  
<br/>  
Maven은 슬프게도 우리가 말한 위의 사례가 똑같이 재현되고 있습니다.  
compile scope안에 있는 모든 dependency들은 그 아래 소비자에게 compile scope를 복사합니다.  
<br/>  
그러나 Gradle은 우리의 디펜던시 오염을 줄이기 위한 더 나은 통제 방법이 있습니다.  
<br/>  

## Use the implementation Configuration  
<br/>  
그래들의 솔루션은 꽤 쉽습니다. 만약 우리가 complie-itme dependency를 가지고 있다면 우리는 compile 구성대신 implementation 구성을 추가하면 끝입니다. (compile 구성은 deprecated 되었습니다.)
<br/>  
<p align="center">
  <img src="/images/dependency_pollution/explicit-dependency_hu97c1dc90e7360b7561a55ffee52d9b8d_64625_700x0_r1esize_q90_box.jpg" alt="book" width="800"/>
<p/>
<br/>  
만약 X에서 A로 가는 디펜던시가 implementation 구성으로 명시되었다면 C는 더이상 A를 compile-time denpendency시기에 참조 할 수 없습니다.  
C는 더이상 실수로 A의 클래스들을 사용할 수 없다 만약 C가 A의 클래스들을 원한다면 우리는 A의 디펜던시를 명시적으로 선언해줘야 됩니다.  
<br/>
만약 우리가 compile-time dependency에 특정한 디펜던시를 노출시키고 싶으면 예를들면 X가 B의 클래스 즉 B의 Api를 사용하고 싶으면 api옵션이 있습니다.  
<br/>  

## 결론  
<br/>  
만약 우리가 고객에게 디펜던시를 노출시키면다면 그들은 아마도 그들의 코드에 대한 통제권을 잃을 수도 있습니다.  
<br/>  
이러한 디펜던시 누락을 유발할수 있는 상태를 체크하는 것은 어려운 작업처럼 보이지만  Gradle's implementation 구성과 함께라면 꽤 쉽습니다.  

<br/>  
<br/>  

<!-- 출처 : https://reflectoring.io/gradle-pollution-free-dependencies/#whats-dependency-pollution-->
