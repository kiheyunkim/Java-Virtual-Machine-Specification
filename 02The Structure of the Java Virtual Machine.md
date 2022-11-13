# Chapter 2. The Structure of the Java Virtual Machine

이 문서는 가상머신에 대해서 명세합니다. Java Virtual Machine의 특정한 구현에 대해서는 묘사하지 않습니다.

Java Virtual Machine을 올바르게 구현하려면, class file format만 읽을 수 있으면 되며, 명세되어있는대로 동작을 하도록 하면 됩니다.
상세 구현에 대한것은 Java Virtual Machine의 스펙의 일부가 아닌것은 구현자의 창의성을 억제하기 때문입니다. 예를 들어 runtime-data 영억의 메모리 레이아웃, 
Garbage-collection 알고리즘 사용, Java Virtual Machine 내부 최적화를 위한 지시어(예를 들어 머신 코드로 번역하는 등의)들은 
구현자의 재량으로 남겨두었습니다.

명세내의 모든 유니코드에 대한 참조는 http://www.unicode.org 의 *The Unicode Standard, Version 6.0.0*와 관련하여 제공됩니다.


## 2.1 The class File Format(클래스 파일 포멧)

Java Virtual Machine로 부터 실행된 컴파일된 코드는  하드웨어 그리고 운영체제에 독립적인 바이너리 포멧으로 표현됩니다. 일반적으로는(필수는 아님) 파일에 저장되며 
class file 포맷으로 알려져있습니다. class 파일은 정확하게 클래스 또는 인터페이스의 표현을 정의하고, byte ordering 같은 플랫폼에 특정된 object(?) 파일 형식에서 당연하게 여겨지는 상세정보 또한 포함합니다.

Chapter 4, "The class File Format"에서 class 파일 형식을 자세하게 다룹니다.


## 2.2 Data Types(데이터 타입)

자바 프로그래밍 언어와 같이, Java Virtual Machhine은 2가지 타입으로 작동합니다: primitive type과 reference type.
이에 상응하게 2가지 형태의 값도 변수에 저장될 수 있습니다. 매개변수로 전달되고, 메소드에 의해 반환되고 작동할 수 있는 두가지 값이 있습니다.
primitive type과 reference type.

Java Virtual Machine은 거의 모든 타입 체크가 런타임에 앞서 컴파일러에 의해 시행되며, Java Virtual Machine에 의해서는 일어나지 않습니다.
primitive 타입의 값들은 reference 타입의 값으로 부터 구분하거나 런타임에 타입을 결정하기 위해서 태그를 달거나 검사를 할 필요가 업습니다. 
대신 Java Virtual Machine의 명령어 집합은 특정한 타입의 값에 대한 작동하도록 의도된 명령어들을 사용하여 피 연산자 유형을 구분합니다.
예를 들어 iadd, ladd, fadd, 그리고 dadd들은 모두 2개의 숫자 값을 받아서 숫자 결과를 만들어내는 Java Virtual Machine의 명령어입니다. 
그러나 각각은 피 연산자로써 특화되어있습니다.: int, long, float, 각각에 대해서. Java Virtual Machine 명령어 집합에서 지원하는 타입의 요약을 보고싶으면 $2.11.1을 보세요

Java Virtual Machine은 객체에 대한 명시적인 지원을 포함합니다. 객체는 클래스 동적으로 할당된 인스턴스 또는 배열입니다. 
객체에 대한 참조는 Java Virtual Machine의 타입 참조로 고려됩니다. type reference의 값은 객체에 대한 포인터로 생각할 수 있습니다.
객체에 대하서는 하나 이상의 참조가 있습니다. 객체는 항상 타입 참조의 값을 통해 전달되고 테스트되며 동작합니다.

## 2.3 Primitive Types And Values(primitive 타입과 값)
Java Virtual Machine에 의해서 지원되는 primitive data type들은 numeric type, boolean type($2.3.4), 그리고 returnAddress type($2.3.3) 입니다.

numeric type은 integral type(#2.3.1)과 floating-point tpye($2.3.2)로 구성됨

* integral 타입이란?
  * byte: 8bit의 부호있는 2의 보수 정수형, 기본값은 0
  * short: 16bit의 부호있는 2의 보수 정수형, 기본값은 0
  * int: 32bit의 부호있는 2의 보수 정수형, 기본값은 0
  * long: 64bit의 부호있는 2의 보수 정수형, 기본값은 0
  * char: 16비트의 부호없는 정수이며 UTF-16으로 인코딩 되어있는 유니코드 평면을 가르키는 부호없는 정수, 기본값은 null code point(\u0000)
* 부동 소수점 타입들은 다음과 같습니다.
  * float: float 값 세트의 요소이거나 지원되는 경우 float-extended-exponent 세트 값, 기본값은 양수 0
  * double: double 값 세트의 요소이거나 지원되는 경우 double-extended-exponent 세트 값, 기본값은 양수 0
* boolean 타입의 경우엔 진위 값을 true와 false로 대응하며, 기본값은 false입니다.

*The First Edition of The Java® Virtual Machine Specification에서는 boolean을 Java Virtual Machine 타입으로 고려하지 않았습니다. 하지만 boolean 값은 제한적으로 Java Virtual Machine에서 지원됩니다.*
*The Second Edition of The Java® Virtual Machine Specification에서는 boolean 타입을 다룸으로서 명확하게 하였습니다.*

returnAddress 타입이란 Java Virtual Machine 지시 명령어를 가르키는 값입니다. primitive 타입들중에, returnAddress 타입만이 Java programming language 타입과 직접 연관되지 않습니다.

### 2.3.1 Integral Types and Values(정수 타입과 값)

Java Virtual Machine에서의 정수 타입의 값은 타음과 같습니다.

* byte: -128 ~ 127(-2<sup>7</sup> ~ 2<sup>7</sup> -1) 양끝 포함
* short: -32768 ~ 32767(-2<sup>15</sup> ~ 2<sup>15</sup> -1) 양끝 포함
* int: -2147483648 ~ 2147483647(-2<sup>31</sup> ~ 2<sup>31</sup> -1) 양끝 포함
* long: -9223372036854775808 ~ 9223372036854775807(-2<sup>63</sup> ~ 2<sup>63</sup> -1) 양끝 포함
* char: 0 ~ 65535 양끝 포함


### 2.3.2 Floating-Point Type, Value Sets, and Values(부동 소수 타입, 값 집합, 값)

부동소수점 타입은 float과 double이며 개념적으로는 32bit 단정밀도와 관련되어있으며 64비트 배정밀도 형식 IEEE 754 값과 동작은 *IEEE Standard for Binary Floating-Point Arithmetic*(ANSI/IEEE Std. 754-1985, New York)
에 명세되어있습니다.

IEEE 754 표준은 양수, 음수 부호크기 방식만을 포함하지 않습니다. 양수 음수 0, 양수 음수의 무한대, 숫자가 아닌 값인 특수한 기호까지도 포함합니다(여기 이후엔 NaN으로 축약)
NaN값은 0 나누기 0과 같은 올바르지 않은 연산의 결과를 표현할 때 쓰입니다.

모든 Java Virtual Machine의 구현에는 float value set과 double value set으로 불리는 2가지의 표준 부동소수점 값 셋을 지원해야합니다.
추가로 Java Virtual Machine 구현해서 선택적으로 float-extended-exponent value set과 double-extended-exponent value set로 불리는 2개 또는 한개의 extended-exponent floating-point value sets를 구현할 수도 있습니다.
이 extended-exponent value set은 특정한 상황에서 표준 value set들이 float또는 dobule 타입의 값을 표현하는데 대신 쓰입니다.

유한의 0이 아닌 부동소수 value set은 s * m * 2<sup>(e-N+1)</sup>형태로 표현될 수 있습니다. s는 +1 또는 -1이고, m은 2N보다 작은 양수이며 e는
E<sub>min</sub>= -(2<sup>K-1</sup> -2), E<sub>max</sub>= 2<sup>(K-1)</sup> -1 경계포함 이고 N과 K는 value set에 따라 정해지는 파라미터입니다.
몇몇 값들은 한 가지 방식보다 많은 형태로 표현될 수 있습니다.; value set에 v라는 값을 s, m 그리고 e값을 표현한 값으로 표현한다고 가정하고 e가 2<sup>K-1</sup>보다 작고
m은 짝수라고 생각하면, m을 반감하고 e를 1만큼 증가시켜도 v와 같은 값을 표현할 수 있습니다. 이러한 형태를 m >= 2<sup>N-1</sup> 일때 정규화 되었다고 말하며, 그 외의 경우 비정규화 되었다고 합니다.
value set내의 값이  m >= 2<sup>N-1</sup>와 같이 나다낼 수 없는 경우에 정규화된 표현이 없으므로 비정규화된 값이라고 부릅니다.

**표 2.3.2-A. Floating-point value set parameters**

| parameter       | float | float-extended-exponent | double | double-extended-exponent |
|-----------------|-------|-------------------------|--------|--------------------------|
| N               | 24    | 24                      | 53     | 53                       |
| K               | 8     | &gt;=11                 | 11     | &gt;=15                  |
| E<sub>max</sub> | +127  | &gt;= +1023             | +1023  | &gt;= +16383             |
| E<sub>min</sub> | -126  | &lt;= -1022             | -1022  | &lt;= -16382             |

하나 또는 모든 extended-exponent value set은 구현되어있는 경우에 지원됩니다. 각각의 extended-exponent value set의 지원은 구현체에 종속된 상수 K에 의해서 지원되며
이 값들은 표 2.3.2-A에 있습니다. 이 K 값들은 E<sub>min</sub>, E<sub>max</sub>값을 정해주게 됩니다.

각각의 value set 4개는 위에 표기된 것처럼 유한의 0이 아닌 값만을 포함, 양수 0, 음수 0, 양수 무한, 음수 무한, NaN 5가지도 포함하고 있습니다.

표 2.3.2-A에에 있는 제약조건들을 보면 float value set의 모든 요소들이 float-extended-exponent value set, double value set, double-extended-exponent valueset에서도 필수 값으로 디자인 되었습니다.
각각의 extended-exponent value set은 표준 value set보다 더 큰 범위의 지수값을 가지고 있지만 더 정밀한 정밀도를 가지지는 않습니다.

float value set의 요소들은 NaN값으로 지정된 하나의 값만 제외하고 IEEE 754 표준에 정의되어있는 단정밀도 소수점 형식만 표현할 수 있습니다.(IEEE 754는 2<sup>24</sup>-2를 NaN 값으로 정의함)
double value set의 요소들은 NaN값으로 지정된 값을 제외하고 IEEE 754에 지정되어있는 배정밀도 소수점 형식을 사용하여 값을 표현할 수 있습니다.(IEEE 754는 2<sup>53</sup>-2를 NaN 값으로 정의함)
그러나 float-extended-exponent와 double-extended-exponent들은 IEEE 754에 표현된 single extended, double extended 각각의 값을 따르라는 요구는 하지않습니다.
이 명세는 부동소수점 값을 class file format($4.4.4, $4.4.5)에 표현해야만 하는 경우를 제외하고 부동소수점 값을 특정값으로 지정하는것을 요구하진 않습니다.

float, float-extneded-exponent, double 그리고 double-extended-exponent value set들은 값이 아닙니다. 이 들은 항상 Java Virtual Machine에서 float 이라는 값의 타입을 표현하기 위해 올바르게 구현되어 있어야합니다.
하지만 float-extended-exponent value set의 요소로 대신 사용하는 것도 허가 됩니다. 비슷하게 double의 경우에도 value type을 사용하기 위해서 항상 정확하게 구현되어있어야 하지만, 
double-extended-exponent value set으로 대신 사용하는 것은 허가됩니다.

NaN값을 제외하고 부동 소수점 값들은 순서가 있습니다. 가장 작은수부터 가장 큰 것 까지 정렬했을때, 음수 무한, 음수 유한 값, 양수, 음수 0, 양수 유한 값, 양수 무한값 순서대로 정렬됩니다.

부동 소수점 양수 0과 음수 0은 동일하게 비교됩니다. 그러나 다른 연산자들은 구분할 수 있습니다. 예를 들어 1.0을 0.0으로 나누면 양수 무한이 되지만, 1.0을 -0.0으로 나누면 음수 무한이 됩니다.

NaN은 순서가 없으며, 숫자적 비교와 숫자적 동일성 비교는 피연잔자가 둘중 하나가 NaN이거나 둘다 인 경우에도 false가 나옵니다.
특히 값이 NaN인 경우에 자신에 대한 동등성에 대한 테스트의 경우에도 false가 나옵니다.
NaN에 대한 비동등성 테스트에 대해서는 true 값을 갖습니다.

### 2.3.3 The returnAddress Type and values(returnAddress 타입) 

return Address 타입은 Java Virtual Machine의 jsr, ret, 그리고 jsr_w 명령어에 의해서 사용됩니다. returnAddress 타입은 Java Virtual Machine 지시어 명령코드를 가르킵니다.
숫자 primitive 타입과는 다르게 returnAddress 타입은 어떤 자바 프로그램 언어와도 대응되지 않으며 프로그램이 동작 중에 변경할 수 없습니다.


### 2.3.4 The Boolean Type(불리언 타임)

Java Virtual Machine에서 boolean 타입이 정의되어있긴 하지만 제한적으로 지원합니다. Java Virtual Machine에는 boolean 값을 연산하는 담당 명령어는 없습니다.
대신에 자바 프로그래밍 언어에서 boolean 값으로 작동하는 표현들은 Java Virtual Machine의 int 데이터 타입으로 컴파일 됩니다.

Java Virtual Machine은 boolean 배열은 지원합니다. newarray 명령어 boolean 배열을 만들 수 있습니다. boolean 타입의 배열은
btre array 명령어 baload와 bastore 명령어를 통해서 접근하고 수정됩니다.

*오라클의 Java Virtual Machine의 구현에서 자바 프로그래밍 언어 내에 boolean 배열은 boolean 요소 각각 8bit를 사용하는 byte array로 인코딩됩니다.*

Java Virtual Machine은 boolean 배열 요소들을 1을 true로 0을 false로 인코딩합니다. 자바 프로그래밍 언어의 boolean 값이 Java Virtual Machine 타입의 int로 매핑되는 경우, 컴파일러는 같은 인코딩을 사용해야합니다.


## 2.4 Reference Types and Value(참조 값 타입과 값)

3가지 참조 타입이 있습니다: 클래스 타입, 배열 타입, 인터페이스 타입. 그들의 값들은 각각 동적으로 생성된 클래스 인스턴스, 배열 또는 클래스 인스턴스 또는 인터페이스를 구현한 배열등을 참조합니다.

array type은 component type으로 1차원 배열(길이는 타입에 의해서 지어지지 않음). 배열 타입의 component 타입 자신도 배열 타입일 수 있습니다.
만약 어떤 배열타입으로부터 시작한다면, 컴포넌트 타입을 고려해야하고 그러고 나면(컴포넌트 타입이 배열 타입이더라도) 그 타입의 컴포넌트 타입을 고려해야하고, 최종적으로는 
배열 타입이 아닌 컴포넌트 타입에 도달해야만 합니다; 이를 배열 타입의 element type 이라고 합니다. 배열 타입의 element type은 primitive 타입, 클래스 타입, 또는 인터페이스 타입이어야 합니다.

reference 타임은 또한 특별한 null 참조를 할 수 있습니다. 어떠한 객체도 참조하고 있지 않다. 를 null로서 표시합니다.
null 참조는 초기에는 런타임 타입이 없지만 어떠한 타입으로도 캐스팅할 수 있습니다. reference type의 초기값은 null 입니다.

이 스펙은 null을 인코딩할 구체적인 값을 요구하지는 않습니다.

## 2.5 Run-Time Data Areas

Java Virtual Machine은 프로그램을 실행하는 동안에 다양한 run-time 데이터 영역을 정의합니다. 몇몇 데이터 영역들은 Java Virtual Machine의 시작에 생성되며 Java Virtual Machine의 종료시에 파괴됩니다.
다른데이터 영역들은 쓰레드마다 존재하며. 쓰레드 마다 있는 데이터 영역들은 쓰레드가 생성될때 생성되며, 종료될때 파괴됩니다.


### 2.5.1 The pc Register(pc 레지스터)

Java Virtual Machine은 한번에 많은 쓰레드의 실행을 지원합니다(JLS $17). 각각의 Java Virtual Machine 스레드는 각자의 pc(program counter) 레지스터를 가지고 있습니다.
이러한 점에서 각각의 Java Virtual Machine 스레드 들은 하나의 메소드 코드를 실행합니다, 즉 스레드에 대한 *current method*($2.6). 
메소드가 만약 native가 아니라면 pc 레지스터는 현재 실행중인 Java Virtual Machine 명령어를 포함합니다. 만약 nvative 메소드를 실행중이라면 Java Virtual Machine의 레지스터는 정의 되지 않습니다.
Java Virtual Machine의 pc 레지스터는 특정 플랫폼의 returnAddress 또는 네이티브 포인터를 가지고 있기에 충분히 넓습니다. 


### 2.5.2 Java Virtual Machine Stacks(자바 가상머신 스택)

각각의 Java Virtual Machine 스레드들은 스레드 생성과 같은 시간에 만들어진 개별 Java Virtual Machine stack을 가지고 있습니다.
Java Virtual Machine stack은 frame($2.6)을 저장합니다. Java Virtual Machine Stack은 C언어와 같은 전통적인 언어의 스택과 유사한데
지역변수나, 결과의 일부를 가지고 있고 메소드의 호출과 반환을 진행합니다. 왜냐하면 Java Virtual Machine stack은 frame이 힙에 할당되어있을떄 frame을 push나 pop하는 것 말고는 어떠한 조작도 불가능하기 때문입니다.
Java Virtual Machine stack은 연속적일 필요 없습니다.

*First Edition of The Java® Virtual Machine Specification에서 Java Virtual Machine stack은 Java stack으로 알려져있습니다.*

이 명세는 Java Virtual Machine stack 각각이 연산에 필요한만큼 동적으로 확장되거나 축소 또는 고정값을 지정하도록 허가합니다.
Java Virtual Machine 스택이 고정사이즈라면, 각각의 Java Virtual Machine 스택은 스택이 만들어졌을 때 톡립적이게 선택할 수 있습니다.

*Java Virtual Machine의 구현은 Java Virtual Machine 스택의 초기 사이즈를 프로그래머 또는 사용자에게 제거권을 줄 수 있을 뿐만 아니라 최대, 최소 사이즈 사이에서 동적으로 확장 축소 하게 할 수 있습니다.*

다음 예외적인 상황들은 Java Virtual Machine stack과 관련이 있습니다.
* **Java Virtual Machine stack에서 허락된 크기보다 더 큰 공간이 필요한 연산의 경우 Java Virtual Machine은 StackOverflowError예외를 던집니다.**
* **Java Virtual Machine stack이 동적으로 확장될 수 있있고, 확장이 시도하려고 하지만. 부족한 메모리가 확장에 영향을 끼치거나, 부족한 메모리가 새로운 쓰레드가 새로운 초기 Java Virtual Machine stack을 만들기에 부족한 메모리를 가진경우 Java Virtual Machine은 OutOfMemoryError 예외를 던집니다.**


### 2.5.3 Heap(힙 메모리)

Java Virtual Machine은 모둔 Java Virtual Machine 스레드들이 공유하고 있는 heap을 가지고 있습니다. 힙은 모든 클래스 인스턴스들과 배열들이 할당되어있는 메모리이며 run-time data area 입니다.

heap은 가상머신이 시작하면서 생성됩니다. Heap은 자동으로 공간을 관리하는 시스템(grabage collector 라고 알려진)으로부터 관리되고 있고, 객체들은 결코 명시적으로 할당해제 될 수 없습니다.
Java Virtual Machine은 자동 공간 관리 시스템의 종류 특정하게 가정하지 않았고 공간관리 기술은 개발자의 시스템 요구사항에 관련되어 선택될 수 있습니다.
heap은 고정사이즈일 수 있으며, 연산의 필요에 따라 확장될 수도 있고, 필요 없어졌을 때 넓어진 힙 영역을 축소할 수도 있습니다.
Heap을 위한 메모리 영역은 연속적일 필요는 없습니다.

*Java Virtual Machine의 구현은 프로그래머 또는 사용자에게 초기 힙 사이즈를 지정하거나, 동적으로 확장이나 축소하는 경우 최대, 최소 힙 사이즈를 지정할 수 있도록 제공할 수 있습니다.*

다음 예외적인 상황들은 heap과 관련이 있습니다.
* **연산이 자동 공간 관리 시스템에 의해서 이용가능한 힙보다 더 큰 사이즈를 필요로 하는경우, Java Virtual Machine은 OutOfMemoryEror 예외를 던집니다.**


### 2.5.4 Method Area(메소드 영역)
Java Virtual Machine은 모든 Virtual Machine 스레드 들이 공유하고 있는 method area를 공유하고 있습니다. 메소드 영역은 평범한 언어를 컴파일한 코드 같은 것과 또는 운영체제 프로세스에서 있는
"텍스트" 조각과 유사한 것들을 저장하는 공간입니다. 메소드 영역은 클래스와 인스턴스 초기화, 인터페이스에서 쓰이는 run-time constant poll, 필드, 메소드 데이터와, mothod의 코드, 생성자, special method를 ($2.9)
클래스별 구조에 저장합니다.

메소드 영역은 가상머신이 시작할때 생성됩니다. 비록 메소드 영역이 논리적으로 힙 영역의 일부이긴 하지만, 간단한 구현으로는 garbage collect나 압축을 선택하지 않을 수 있습니다.
이 명세는 메소드 영역의 위치나 컴파일된 코드를 관리하는 정책에 대해서는 요구하지 않습니다. 메소드 영역은 고정된 사이즈로 될 수 있거나, 연산에 의해서 확장되야 한다면 늘어나고
불필요한 메소드 영역이 늘어나면 줄어들 수 있습니다. 메소드 영역에 필요한 메모리 영역은 연속적일 필요는 없습니다.

*Java Virtual Machine의 구현은 사용자에게 메소드 영역의 초기사이즈를 사용자나 개발자에게 제어하도록 제공할 수 있을 뿐만 아니라. 메소드 영역의 변경되는 경우에 대해 메소드 영역 최대 최소 사이즈를 통해 조정할 수 있습니다.*

다음 예외적인 상황들은 메소드 영역과 관련 있습니다.
* **메소드 영역이 할당 요청을 받았을때 수행할만큼의 메모리가 없는 경우에, Java Virtual Machine은 OutOfMemory Error예외를 던집니다.**


### 2.5.5 Run-Time Constant Pool(런타임 상수 풀)

run-time constant pool은 클래스파일내에 있는 클래스별 또는 인터페이스별로 constant_pool 테이블의 표현입니다.
run-time constant pool은 몇몇 상수 종류를 포함하는데, 컴파일 타입에 알려준 숫자 리터럴 부터, 런타임이 해석되어야할 필드 참조등을 포함합니다.
run-time constant pool은 전통적인 프로그래밍 언어에서 제공되는 심볼 테이블과 비슷한 기능을 제공합니다. 물론 전형적인 심볼 테이블 보다는 더 범용적인 데이터를 포함하고 있습니다.

각각의 run-time contant pool은 Java Virtual Machine의 메소드 영역에 할당되어 있습니다. 클래스나 인터페이스를 위한 run-time constant pool은 Java Virtual Machine에 의해서
클래스 또는 인터페이스가 생성됐을 때 생성됩니다.

다음의 예외적인 상황은 클래스,또는 인터페이스를 위한 run-time constant pool 생성과 연관이 있습니다.
* 클래스나 인터페이스를 만들때 run-time constant pool의 생성에 필요한 메모리가 Java Virtual Machine의 메소드 영역에 있는 공간보다 더 많은 메모리가 필요한 경우 Java Virtual Machine은 OutOfMemoryError 예외를 던집니다.

### 2.5.6 Native Method Stacks

Java Virtual Machine의 구현은 native 메소드(자바 프로그래밍 언어 말고 다른 언어로 쓰여진 메소드)를 지원하기위해 "C stacks"로 불리는, 전통적인 스택을 사용할 수 있습니다.
네이티브 메소드 스텍을 C와 같은 언어로 되어있는 Java Virtual Machine 명령어 셋의 해석기 구현에도 사용할 수 있습니다.
네이티브 메소드를 불러올 수 없고, 네이티브 메소드 스택을 제공할 필요가 없는 Java Virtual Machine의 구현에서는 native method stacks를 제공할 필요가 없습니다.
만약 제공된 경우에는 각 쓰레드가 생성된 때에 Native Method stack은 각 쓰레드 별로 할당되게 됩니다.

이 명세는 Native method stack이 고정된 크기를 가지거나, 연산에 의해 필요한 경우 동적으로 확장하거나 축소하는 것을 허가합니다.
네이티브 메소드 스택이 고정값인 경우 각각 스택이 생성되었을때 각각의 native method stack의 사이즈는 각 스레드가 생성될 때, 독립적으로 선택될 수 있습니다.

*Java Virtual Machine의 구현은 프로그래머 또는 사용자에게 native method stacks의 초기 사이즈를 개발자나 사용자에게 제어할 수 있도록 제공해줄 뿐만아니라, native method stack의 크기가 조정이 필요한 경우 최대 최소 사이즈를 통해 조정할 수 있습니다.*

다음의 예외적인 상황은 native method stacks와 관련이 있습니다..
* thread가 허가된 native method stack보다 큰 여난을 하려하는 경우 Java Virtual Machine은 StackOverflowError예외를 던집니다.
* native method stack이 동적으로 확장될 수 있고 native method stack의 확장이 가능한 메모리보다 부족할때 시도되거나, 새로운 메소드를 위한 초기 native mthod stack을 생산할 메모리가 부족한 경우
  Java Virtual Machine은 OutOfMemoryError 예외를 던집니다.


## 2.6 Frames

frame이란 dynamic linking, 메소드 값 반환, 예외전달을 이행하는데 쓰는 데이터나 결과의 일부분을 저장하는데 사용합니다.

새로운 frame은 메소드가 호출될 때마다 만들어집니다. frame은 호출이 종료되면 파괴됩니다, 종료가 정상이거나 그렇지 않은경우(catch되지 않은 예외를 던질때).
frame은 스레드가 만든 프레임의 Java Virtual Machine stack으로 부터 할당됩니다. 각각의 프레임은각자의 지역변수($2.6.1)배열을 가지고 있고, 개별의 피연산자 스텍을 가지고 있으며,
현재 메소드의 클래스의 run-time constant pool을 참조합니다.

*프레임은 추가적인 구현별 정보로 확장될 수 있습니다. 디버깅 정보와 같은*

지역 변수 배열의 크기와 연산자 스택은 컴파일 타임에 결정되며 프레임($4.7.3)과 연관된 메소드의 코드와 함께 제공됩니다.
따라서 frame 데이터 구조의 사이즈는 Java Virtual Machine의 구현에 따라 정해지고 메소드의 호출되고 동시에 이 구조를 위한 메모리가 할당됩니다.

메소드 실행을 위한 하나의 프레임은 스레드의 제어가 주어졌을 때, 어느시점이든 활성화 됩니다. 이 프레임은 current frame을 참조하며, 이 메소드는 current method로 알려져있습니다.
current method에 있는 클래스는 current class 입니다. 지역 변수와, 피연산자 스텍의 작업은 전현적으로 current frame을 참조합니다.

current frame의 다른 메소드를 호출하거나 이 메소드가 끝났다면 frame은 current가 될 수 없습니다. 메소드가 호출되었을 때, 새로운 frame은 생성되며 
새로운 메소드로 제어가 옮겨가면서 current가 됩니다. 메소드가 반환을 하고나면 current frame은 이전 프레임이 있으면 메소드를 호출의 결과를 돌려줍니다.
현재 프레임은 그후 폐기되며 이전 프레임이 current가 됩니다.

쓰레드에 의해서 만들어진 frame이 해당 스레드에서 로컬이며, 다른 쓰레드에 의해서 참조 될 수 없음을 주목하세요


### 2.6.1 Local Variables(지역 변수)

각 frame($2.6)은 local variables로 알려져있는 변수 배열을 포함하고 있습니다. frame의 local variable의 배열 길이는 컴파일 타임에
결정되며 프레임($4.7.3)과 연관되어있는 메소드의 코드와 함께 클래스와 인터페이스의 이진 표현을 공급해줍니다.

하나의 local variable은 boolean, byte, char, short, int, float, reference 또는 returnAddress 타입의 값을 잡아둘 수 있습니다.
2개의 local variable은 long 또는 double타입을 잡아둘 수 있습니다

Local variable은 인덱스를 통해 처리됩니다. local variable의 첫 인덱스는 0입니다. 정수(인덱스)는 0과 local variable 배열보다 하나 작은 숫자 사이인 경우에만 local variable의 인덱스로 간주됩니다.

long과 double타입의 값은 2개의 연이은 local variable을 차지합니다. 그러한 값은 더 작은 인덱스로 처리해야만 합니다. 예를 들어 double 타입의 값은 local variable 배열의 인덱스 n에 저장되지만
실제로는 인덱스 n과 n+1을 차지하고 있습니다. 그러나 n+ 1인덱스의 local variable은 불러올 수 없습니다. 저장은 할 수 있습니다. 그러나 그렇게 하면 n인덱스의 local variable은 무효화 됩니다.

Java Virtual Machine은 n이 짝수가 되는걸 요구하지는 않습니다. 직관적인 용어로 long과 double은 타입의 값은 64비트로 정렬될 필요는 없습니다.
구현자는 값에대해 2개의 local variable을 사용하는 것을 표현하는 적절한 방법을 자유롭게 결정할 수 있습니다.

Java Virtual Machine은 메소드 실행에 매개변수 전달을 할때 local variable을 이용합니다. 클래스 메소드 실행에서 어떤 매개변수들도 local variable 0으로 부터 시작하는 연속적인 local variable들을 전달합니다.
인스턴스의 메소드가 호출되었을 때, local variable 0은 항상 인스턴스 메소드가 호출된 객체(자바 프로그래밍 언어에서 this)에게 참조를 넘겨줍니다.
파라미터들은 local variable 1부터 순서대로 전달되게 됩니다.

> 확인 해보려면 javac .\Test.java -g:vars
> this가 첫 번쨰, 그다음 파라미터, 그다음 내부 변수들이 나옴.


### 2.6.2. Operand Stacks(피연산자 스택)

각각의 프레임들은 operand stack으로 알려진 last-in-first-out(LIFO)스택을 포함합니다. 프레임의 피연산자 스택의 최대 깊이는 컴파일 타임에 결정되며 frame과 련관된 메소드의 코드와 함께 전달됩니다.($4.7.3)

컨텍스트가 명확할떄 우리는 현재 프레임의 피연산자 스텍을 operand stack이라고 추정합니다.

frame이 operand stack을 생성했을때는 비어있습니다. Java Virtual Machine은 피연산자 스택에 로컬 변수나 필드로 부터 값이나 상수를 불러오기 위한 명령어를 제공합니다.
다른 Java Virtual Machine 명령어는 피연산자 스택에서 피연산자를 가져와서, 실행하고, 피연산자 스택으로 결과값을 밀어 넣습니다.
피연산자 스텍은 또한 메소드에 전달할 매개변수를 준비하고 메소드의 결과를 받는데에 사용됩니다.

예를들어 *iadd*명령어는 2개의 int 값을 더합니다. iadd명령어는 연산이전에 피연산자 스텍의 위에 int값 2개가 추가되있어야합니다. 2개의 int 값은 피연산자 스택으로부터 pop 됩니다.
2개 값이 더해지고, 합계를 피연산자 스택으로 다시 들어가게 됩니다. 부연산들은 피연산자 스텍에서 중첩될 수 있으며, 결과적으론 중첩된 연산에서 사용될 수 있는 값이 됩니다.

피연산자 스택에 들어간 각각은 long과 double 타입을 포함한 어떠한 Java Virtual Machine 타입의 값을 붙잡아 둘 수 있습니다.

피연산자 스택으로 부터 나온 값들은 그들의 타입에 적절한 방식으로 연산되어야합니다. 불가능 하다면, 예를 들어 2개의 int 값을 넣고
그 후 그것들은 long처럼 다루거나, 2개의 float 값을 넣고 iadd 명령어를 통해서 그들을 더하는 경우입니다. 적은 수의 Java Virtual Machine 명령어들은(dup 명령어와 swap 명령어)
특정 타입에 관련없이 원시 값처럼 run-time data area에서 동작합니다; 이 멸영어들은 개별 값들은 파괴하거나 수정하는데는 사용할 수 없게 정의됩니다.
이런 피연산자 스택 조작에 대한 제한은 class 파일 검증을 통해 싱행됩니다.

어느 시점에서도 피연산자 스택은 타입 연관 깊이를 가지고 있습니다. long또는 double일 때는 2단위를 기여하고, 그 외의 타입은 1단위의 깊이를 기여합니다.


### 2.6.3 Dynamic Linking (동적 링크)*

각각의 frame은 메소드 코드의 dynamic linking을 지원하기 위해 현재 메소드의 타입에 대한 run-time constant pool 참조를 포함하고 있습니다.
메소드에 대한 클래스 파일 코드는 호출할 메소드를 참고하고 변수들은 symbolic reference를 통해서 접근하게 됩니다. 동적 링킹은 이런 symbolic 메소드 참조를 
구체적인 메소드 참조로 번역하고, 아직 정의되지 않은 심볼들을 해석하는데 필요한 클래스들을 로딩하고, 변수들의 런타임 상의 위치와 관련된 적절한 저장 구조의 오프셋을 통해 접근할 수 있도록 해석합니다.

메서도와 변수에 대한 지연 연결은 메소드를 사용하는 다른 클래스를 변경하기 때문에 코드를 손상시킬 확률이 낮습니다.


### 2.6.4 Normal Method Invocation Completion(정상 메소드 호출 완료)

메소드 호출 정상 완료란 Java Virtual Machine으로부터 직접 발생하거나 명시적인 throw 문에 의해서 발생된 예외가 발생하지 않았음을 의미합니다.
현재 메소드의 호출이 정삭적으로 완료되면 호출된 메소드로 값이 리턴됩니다. 이것은 반환해야하는 값이 있는 경우에 호출된 메소드에게 return($2.11.8) 명령어를 실행하게 됩니다.

current frame은 이 경우에 local variable과 피연산자 스택을 포함한 호출자의 상태를 저장하고 있고, 호출자의 program counter을 적절하게 증가시켜 과거 메소드 호출 명령을 통과하게 합니다.
실행 이후 반환값(있다면)과 함께 메소드의 프레임을 정삭적으로 계속 실행하며 해당 프레임의 피연산자 스택으로 push합니다.


### 2.6.5 Abrupt Method Invocation Completion(갑작스러운 메소드 호출 완료)

갑작스러운 메소드 호출 완료는 메소드 내에 Java Virtual Machine 명령어 실행했는데 Java Vritual Machine은 예외를 던지고 이 예외가 처리되지 않았을때 발생합니다.
athrow($athorw)명령어의 실행 또한 명시적인 예외 던져짐의 원인이 되며, 예외가 현재 메소드에서 처리되지 않은경우 갑작스러운 메소드 호출 완료의 원인이 됩니다.
갑작스러게 메소드 호출이 완료되는경우 호출자에게 값은 반환되지 않습니다.

## 2.7 Representation of Objects(객체의 표현)

Java Virtual Machine은 객체에 대해 특정한 내부 구조를 요구하지 않습니다.

*몇 Oracle의 Java Virtual Machine의 구현에서 클래스 인스턴스의 참조는 포인터의 한 쌍으로 다루어집니다.; 하나는 객체의 메소드와 객체의 타입을 나타내는 Class 객체에 대한 포인터를 포함하는 테이블이고 또 하나는 객체 데이터를 위한 힙으로 부터 할당된 메모리입니다.*


## 2.8 Floating-Point Arithmetic

Java Virtual Machine은 *IEEE Standard for Binary Floating-Point Arithmetic (ANSI/IEEE Std. 754-1985, New York).*에 명세된 부동소수점 연산의 하위집합을 통합합니다.


### 2.8.1 Java Virtual Machine Floating-Point Arthmetic and IEEE 754

Java Virtual Machine과 IEEE 754 표준에서 부동 소수점 연산의 차이는 다음과 같습니다.

* Java Virtual Machine의 부동 소수점 연산은 예외, trap(유저레벨 프로세스 예외)가 없습니다. 이에 반해 IEEE 754에서는 
  0으로 나누기, 오버플로우, 언더플로우와 같은 잘못된 연산이나 부정확함에 대한 예외적인 상황으로 신호합니다. Java Virtual Machine은 NaN 값에 대해서 신호를 보내지 않습니다.
* Java Virtual Machine은 IEEE 754의 부동소수점 비교 signaling을 지원하지 않습니다.
* Java Virtual machine의 반올림 연산들은 항상 IEEE754 반올림에 가장 가까운 방식을 사용합니다. 부정확한 결과는 최하위 비트를 0으로 바꾸어 가장 가까운 값으로 표현합니다.
  이것은 IEEE 754의 기본 동작입니다. 하지만 Java Virtual Machine의 부동소수점을 정수값으로 변환하는 명령어는 0으로 반올림 시킵니다.
  Java Virtual Machine은 부동 소수점 반올림 방법을 변경할 수 있는 방법을 제공하지 않습니다.
* Java Virtual Machine은 double-extended-exponent value set이 single extended format을 지원한다고 할때를 제외하고는  IEEE 754의 single extended 또는 double extended 형식을 지원하지 않습니다.
  선택적으로 지원되며 iEEE 754 extended 형식의 값에 대응될 필요가 없습니다: IEEE 754 extended 형식은 extended exponent range만큼의 확장된 정밀도를 요구합니다.

### Floating-Point Modes(부동 소수점 방식)

모든 메소드는 FP-strict 또는 not FP-strict라는 부동 소수점 방식을 가집니다.메소드의 부동소수점 방식은 메소드에 정의된 *method_info* 구조의 *access_flag*항목의 ACC_STRICT flag를 통해서 결정됩니다.
flag가 설정된 경우에는 FP-strict, 아닌 경우엔 not FP-strict 메소드가 됩니다.

*이 ACC_STRICT 플레그에 대한 매핑은 JDK release 1.1이나 이전 릴리즈에 대해서 클래스내에 메소드에 대해서 not FP-strict가 아님을 의미합니다.*

피연산자 스텍을 포함한 프레임을 호출한 메소드가 floating-point 방식을 가지고 있을때 피연산자 스텍이 floating-point 방식을 가지고 있다고 추정합니다.
비슷하게, 메소드가 floating-point 방식을 가진 명령어를 가질 때, Java Virtual Machine 명령어도 floating-point 방식을 가진다고 추정할 것입니다.

float-extended-exponent value set이 지원되는 경우, FP-strict가 아닌 피연산자 스텍의 float 타입의 값은 value set 변환에 의해서 금지되는 경우를 제외하고
value set의 범위를 넘을 수 있습니다. double-extended-exponent value set이 지원되는 경우, FP-strict가 아닌 피연산자 스텍의 double 타입의 값은 value set 변환에 의해서 금지되는 경우를 제외하고
value set의 범위를 넘을 수 있습니다.

다른 모든 문맥에서, 피연산자 스택이든 다른 곳이든, floating-point 방식에 상관없이 floating-point 타입 float과 double의 값은 각각 float vluae set과 double value set을 넘을 수 있다.
특별히 클래스와 인터페이스 필드, 배열 요소, local variable, 그리고 메소드 매개변수들은 표준 value set으로부터 가져온 값만 포함할 수 있습니다.
