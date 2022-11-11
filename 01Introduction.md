# Chapter 1. Introduction

## A Bit of History(짧은 역사)

Java® 프로그래밍 언어는, 범용, 동시적, 객체 지향 언어입니다. 문법은 C와 C++과 비슷하지만, C나 C++을 복잡하고 어렵고 안전하지 않게 만드는 기능들은 배제되어 있습니다. 자바 플랫폼은
네트워크로 연결되어있는 고객 장치들의 소프트웨어를 만드는 문제를 해결하기 위해서 제작되었습니다. 자바는 여러 호스트아키텍쳐를 지원하고 안전한 소프트웨어 구성요소 전송을 위해 디자인 되었습니다.
이러한 요구사항들을 충족하기위해, 컴파일된 코드들은 네트워크를 통해 전송되었어도 살아남아야하며, 어떠한 클래이언트에서도 동작하고, 클라이언트에서 돌아가기에 안전하다는 것을 확신해야했습니다.

World Wide Web의 대중화에 따라 이러한 특성들은 더 흥미로웠습니다. 웹 브라우저들은 수백만명의 사람들이 네트워크를 서핑하고 풍부한 미디어 컨텐츠들이 쉽게 다가갈 수 있게 했습니다. 
마침내 느린 모뎀이든 빠른 네트워크들을 사용하든는 어떤 기계든 상관없이 본질적으로 당신이 보고 듣는 매체는 본질적으로 동일했습니다.

Web 열광자들은 곧 Web의 HTML 문서에 의해 지원되는 컨텐츠가 제한된 다는것을 발견했습니다. forms와 같은 HTML 확장들은 제한만을 강조했으며, 
어떤 브라우저도 유저들이 원하는 기능을 포함하지 않은다는 것을 분명히 했습니다. 확장성이 답이었습니다.

HotJava 브라우저는 HTML페이지 내에 심어질 수 있는 프로그램으로서 자바 프로그래밍 언어와 플랫폼의 흥미로운 속성을 보여준 첫 소개 였습니다.
프로그램들은 HTML 페이지가 불러와지면서 투명하데 다운로드 됩니다. 인터넷 브라우저로 부터 수용되기 전에 프로그램은 안전한 프로그램인지 확인을 합니다.
HTML페이지와 같게 컴파일된 프로그램들은 네트워크, 그리고 호스트에 비종속적이었습니다. 프로그램들은 어느 종류의 머신에 도착했는지에 상관없이 같은 방식으로 행동했으며, 불러와지고 동작했습니다.

자바플랫폼과 결합된 웹 브라우저는 미리 결정되어있는 기능의 집합으로 더이상 제한되지 않습니다. 동적 컨텐츠와 결합된 웹 페이지의 방문자들은 자신들의 머신이 컨텐츠로 부터 손상되지 않을 것이라는 것을 확신할 수 있었습니다
프로그래머들은 프로그램을 한번 작성할 수 있으며, Java run-time 환경이 제공되는 어떠한 머신에서도 실행됩니다.

## The Java Virtual Machine (자바 가상 머신)

Java Virtual Machine은 자바 플랫폼의 중추석입니다. 하드웨어, 운영체제에 비 종속적이며, 작은 크기의 컴파일된 코드, 의심스러운 프로그램으로 부터 사용자를 보호할 수 있는 기술의 구성요소 입니다.

Java Virtual Machine은 추상화된 컴퓨터입니다. 실제 컴퓨터 처럼, 런타임 동안에 다양한 메모리를 조작하고 설정할 수 있는 명령어들을 가지고 있습니다. 가상머신을 이용해서 프로그래밍 언어를 만드는 것은 상당히 흔합니다.;
가장 잘 알려진 가상 머신은 UCSD Pascal의 P-Code 머신일 것입니다.

Sun MicroSystems에서 진행한 첫번째 Java Virtual Machine의 구현은 현대의 PDA(Personal Digital Assistant)와 유사한 휴대용 장치로부터 호스팅되는 소프트웨어에서 Java Virtual Machine 명령어 셋을 에뮬레이트했습니다.
오라클의 현재 구현은 Java Virtual Machine을 모바일, 데스크톱, 서버 장비에서 에뮬레이트 합니다. 하지만 Java Virtual Machine은 특정한 구현 기술, 호스트의 하드웨어, 호스트의 OS를 가정하지 않습니다.
특정한 방식으로 해석되지는 않지만, CPU의 명령어로 컴파일링되어 구현될 수 있으며. 반도체에 직접 들어가는 마이크로코드로 구현될 수도 있습니다.

Java Virtual Machine은 자바 프로그래밍 언어에 대해서는 모르고 class 파일 포맷으로 되어있는 특정한 바이너리 포멧만 알고 있습니다.
class 파일은 Java Virtual Machine의 명령어(또는 바이트 코드)와 심볼릭 테이블과 같은 보조정보를 포함하고 있습니다.

보안을 위해, Java Virtual Machine은 class파일내의 코드에 구문적, 구조적인 강력한 제한을 부가합니다. 그러나, 유효한 클래스 파일로 표현될 수 있는 기능을 가진 어떠한 언어라도 Java Virtual Machine에 호스팅될 수 있습니다.
일반적으로 이용가능하고, 머신 플랫폼에 독립성에 매료된 다른 언어의 구현자는 해단 언어의 전달 수단으로 Java Virtual Machine으로 사용할 수 있습니다. 

여기 명세되어있는 Java Virtual Machine은 Java SE 8 플랫폼과 호환되며,  _The Java Language Specification, Java SE 8 Edition_ 에 명세된 자바프로그래밍 언어를 지원합니다.


## Organization of the Specification(스펙의 구성)
* Chapter2: Java Virtual Machine 아키텍쳐에 대한 개요
* Chapter3: 자바 프로그래밍 언어로 작성된 코드를 Java Virtual Machine의 명령어 세트로 컴파일 하는 방법
* Chapter4: class 파일과 클래스와 인터페이스를 나타내는데 사용되는 운영체제와 하드웨어에 독립적인 바이너리 형식에대한 명세
* Chapter5: Java Virtual Machine의 시작과 loading, linking, 클래스와 인터페이스의 초기화에 대한 명세
* Chapter6: Java Virtual Machine의 명령어 셋을 명세하고 opcode 니모닉을 알파벳 순서대로 표시합니다.
* Chapter7: opcode value를 통해 색인된 Java Virtual Machine opcode 니모닉의 테이블을 제공합니다.

*The Java® Virtual Machine Specification*의 두 번째 판에서 Chapter2는 Java Virtual Machine의 명세를 돕기 위한 자바 프로그래밍 언어의 개요를 제공하지만, 스펙의 일부는 아닙니다.
_Java Virtual Machine Specification, Java SE 8 Edition_ 에서 독자들은 자바 프로그래밍 언어에 대한 정보를 _The Java Language Specification, Java SE 8 Edition_ 로 부터 인용하세요. 
$x.y 형태로 어느 부분이 필요한지 표시되어있습니다.


_The Java® Virtual Machine Specification_ 의 두 번째 판에서 Java Virtual Machine 쓰레드가 메인 메모리를 공유하는 상호작용을 설명하는 로우 레벨 작동을 설명합니다.
_The Java Virtual Machine Specification, Java SE 8 Edition_ 에서 독자들은 쓰레드와 락에 대한 정보를 위해 _ The Java Language Specification_ 의 Chapter 17을 인용하세요.
Chapter 17은 JSR 133 Expert Group에 의해서 만들어진 _The Java Memory Model and Thread Specification_ 을 반영하고 있습니다.


## Notation(표기법)
이 명세를 통틀어서 우리는 Java SE 플랫폼 API로 부터 가져온 클래스와 인터페이스들을 인용합니다.단일 클래스나 인터페이스를 (또는 예시 내에 선언된 것을 제외하고)인용하는 경우 단일 식별자 N을 사용하며,
java.lang. 패키지 내에 N이라는 이름의 인터페이스나 클래스를 참조하는걸 의도합니다. java.lang.을 제외한 패키지로부터 가져온 클래스나 인터페이스는 정규 풀네임을 사용합니다.

java또는 이것의 서브 패키지에 선언된 클래스나 인터페이스를 참조할 때는, bootstrap class loader($5.3.1)로 부터 로드된 클래스나 인터페이스로 생각합니다.

이 스펙에서 사용하는 폰트는 다음과 같습니다.
* 고정폭 폰트는 Java Virtual Machine 데이터 타입, 예외, 에러, 클래스 파일 구조, Prolog코드, 자바코드 조각으로 쓰입니다.
* *이텔릭 체*는 Java Virtual Machine "assembly 언어"와 Java Virtual Machine run-time data 영역에서 쓰이는 항목인 "assembly language로 쓰이고", 명령 코드, 피연산자에 쓰입니다. 또 새로운 용어나 강조를 위해서도 쓰입니다.

사양을 더 명확히 하기위한 비규범적 정보는 작은 글자로 의도됩니다.

## Feedback(피드백)
독자들은 *The Java® Virtual Machine Specification*내의 기술적 문제나 모호함을 jls-jvms-spec-comments@openjdk.java.net로 제보해주세요

javac(자바 프로그래밍 언어를 위한 컴파일러관련)로부터 조작되거나 생성된 class 파일에 관련된 질문은 compiler-dev@openjdk.java.net로 보내주면됩니다.


[원본 링크](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-1.html)