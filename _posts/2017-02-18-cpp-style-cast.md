---
layout: post
comments: true
title: C++ 형 변환 연산
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

C에서 명시적으로 지원하는 형변환은 (type)expression의 형태를 가집니다.  
하지만 서로 유사성도 없는 자료간에도 형변환이 가능하다는 점에서 엄격하지 못한 형변환으로 인한 실수가 발생할 수도 있습니다.  

물론 C++에서도 여전히 이러한 형변환 방법은 지원되고 있으며 클래스에 대한 형변환도 지원합니다.  
클래스에 대한 포인터/레퍼런스의 형변환에서는 상위클래스로의 형변환은 타당합니다. (Upcasting)  
탈 것 클래스와 이것으로 부터 상속받은 자동차 클래스가 있다면 탈 것 클래스에 대한 포인터는 자동차 클래스의 개체를 가리킬 수 있습니다.  
"트라제는 탈 것이다."는 자명합니다.  
탈 것에 대한 포인터가 트라제를 가리킬 수 있다는 것입니다(트라제는 이 글의 원작자님이 타는 차입니다).

```
탈것* p = (탈것 *)트라제;  상위클래스 포인터/레퍼런스로의 형변환은 타당하면서도 적법합니다.

탈것 날아다니는것;
자동차* p = (자동차*)날아다니는것; 이것은 타당한가?
이 경우 문법적으로는 유효하지만 타당하지는 않다.(DownCasting 의도)
 ```

C++에서는 4가지의 형변환 연산자를 추가로 지원하여 좀 더 명확하고 엄격한 형변환이 가능합니다.  
이 형변환 연산자들을 사용하여 컴파일러에게 명확한 의도를 알리게 되고 컴파일러는 프로그래머가 하려고 하는 것을 검사할 수 있는 기회를 가지게 됩니다.  

```
const_cast<type>(expr)
dynamic_cast<type>(expr)
reinterpret_cast<type>(expr)
static_cast<type>(expr)
```

# dynamic_cast

포인터나 레퍼런스의 형변환에서 (type)을 사용하여 형변환을 할 경우 보통 상위 자료형(클래스)으로의 형변환만 유효합니다.  
하지만 코드의 실행 중에 다형성으로 인해 하위의 자료형으로 형변환이 이루어져야 할 필요가 생깁니다.  
이때 dynamic_cat<type>(expr)을 사용해서 형변환의 가능여부를 판단 할 수가 있습니다.  
dynamic_cast가 가능하다면 null이 아닌 포인터를 리턴하고, 불가능하다면 null을 리턴하기 때문입니다.  
레퍼런스를 dynamic_cast할 경우 널에 해당하는 레퍼런스가 없으므로 bad_cast 예외를 발생시킵니다.  

High클래스와 High클래스에서 상속받은 Low 클래스가 있다면
```
High* pHigh;
Low low;
pHigh = (High*)low;
```

위의 형변환(Upcasting)이 가능합니다.  
만약 코드 실행 중에서 pHigh를 만났다면 이것은 High형 이나 Low형 개체중 어떤 것을 가리키고 있을 것이다.  
만약 이 pHigh가 가리키는 개체가 Low형이라면 pHigh를 (Low*)로 형변환 가능해야 할 것이다.    
pHigh가 가리키는 대상이 High형이라면 (Low*)의 형변환은 적법하지 않습니다.  

따라서, dynamic_cast<Low*>(pHigh)의 형태로 형변환 하게 되면 pHigh가 가리키는 대상이 Low형이라면 타당하게 형변환된 결과 값을 주고, High라면 null 값을 줍니다.  

만약, FuncOnlyLow()라는 함수가 Low에만 정의된 멤버함수일 때, pHigh를 통해서 이를 호출하려면

```
if( ptr=dynamic_cast<Low*>(pHigh) )
   ptr->FuncOnlyLow();
```

위와 같은 코드를 수행하면 되겠습니다.

# const_cast

이것의 용도는 간단한데 오직 const 또는 volatile의 속성을 변경하기 위한 것입니다.  
오직 같은 자료형에서 const 또는 volatile 속성의 유무 만을 바꾸는 것입니다.  

```
const int *t;  
```

위에서 t가 가리키는 대상은 변경할 수가 없습니다.
하지만 아주 가끔 t가 가리키는 대상 자료가 변경되어야 한다면 어떤 방법을 써야할까요?  

```
int *tt = const_cast<int *>(t);
*tt = 100;

```
이렇게 const_cast를 통해서 변경 가능합니다.  

주의할 점은 포인터의 대상에 대한 접근성은 바꿀 수 있지만 포인터의 대상이 원래 const로 선언된 것이라면 변경을 시도한 후의 결과는 알 수 없습니다(표준이 undefined라는군요).

```
int a;
const int* t;
int* tt = const_cast<int *>(t);
*tt = 10;    // 잘 동작

const int a = 100;
const int* t;
int* tt = const_cast<int*>(t);
*tt = 10;      // undefined 동작. 결과를 알 수 없다. (표준)
```

# reinterpret_cast

이것은 약간은 위험한 형변환 연산자입니다.  
완전히 다른 자료형간의 변환을 프로그래머가 원할 경우 명시적으로 reinterpret_cast를 지정하면 기존의 자료를 새롭게 해석(?)해서 근본적으로 다른 형임에도 강제로 형변환 해버리는 것입니다.  
간단한 예로 포인터를 정수로 바꾸는 것과 같은 동작이 되겠습니다.  
물론 (int)연산자로 캐스팅해도 되지만 프로그래머가 강제로 바꾸는 것을 의도한 다는 것을 컴파일러에게 알리기 위해서 사용합니다.  

좀더 복잡한 경우는 구조체에 대한 포인터를 정수에 대한 포인터로 바꾼다든가 하는 경우이겠습니다.  
문제는 같은 C/C++의 문법을 가지고 코딩을 하더라도 프로그램의 수행환경에 따라서 다른 결과가 나올 수도 있다는 것이다(메모리의 사용이 대표적입니다. 빅엔디안, 리틀엔디안...).  

reinterpret_cast는 원천적으로 서로 다른 (호환이 되지 않는) 포인터 간의 형변환의 용도로 사용합니다.  


# static_cast
static_cast는 dynamic_cast와 대조되는 형변환 연산으로 다형성을 지원하지 않습니다(안정성을 보장하지 않습니다).  
대신 dynamic_cast보다 약간 더 빠릅니다.  
static_cast<type>(expr)에서 type이 암시적으로 expr로 또는 expr이 type로 변환이 가능하다면 적법하고 유효한 것으로 판정됩니다.  
표준의 기본자료형간의 변환도 암시적으로 이루어질 수 있는 것이기 때문에 static_cast가 유효합니다.  
이것의 유효성에 대한 검사는 컴파일시에만 이루어집니다.  
DownCasting에 명시적으로 사용하면 됩니다.  

```
부모 클래스 포인터가 부모 클래스를 가리키는 것은 가능.
자식 클래스 포인터가 자식 클래스를 가리키는 것도 가능.
부모 클래스 포인터가 자식 클래스를 가리키는 것도 가능.
자식 클래스 포인터가 부모 클래스를 가리키는 것도 가능. // dynamic_cast는 안됨

```

출처: http://blog.naver.com/ranken/20047660848

# 프로그래머가 정의하지 않아도 발생하는 예외

bad_cast 예외는 dynamic_cast 연산자를 이용한 형 변환의 과정에서 발생할 수 있는 예외입니다.  

```
struct Foo { virtual ~Foo() {} };
struct Bar { virtual ~Bar() {} };

int main()
{
    Bar b;
    try {
        Foo& f = dynamic_cast<Foo&>(b);
    } catch(const std::bad_cast& e)
    {
        std::cout << e.what() << '\n';
    }
}
결과:
Bad dynamic cast
```

what 함수는 예외의 원인을 문자열의 형태로 반환합니다.  
