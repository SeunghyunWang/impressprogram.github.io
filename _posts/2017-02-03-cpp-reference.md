---
layout: post
comments: true
title: C++ 참조자에 대한 이야기
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 참조자의 기본 이야기
참조자는 자신이 참조하는 변수를 대체할 수 있는 또 하나의 이름입니다.  
참조자는 자신이 참조하는 변수의 주소값을 공유함으로써 그 변수에 대한 접근 방법을 다양하게 하도록 도와줍니다.  
즉 아래의 num1과 num2는 완벽히 동일한 주소값을 갖습니다.  
```
int num1 = 100;
int &num2 = num1;
num2 = 200;
cout << num1;
결과 : 200
```
그리고 참조자를 참조자로 선언할 수 있고 한개의 변수에 대해 여러개의 참조자를 선언할 수도 있습니다.  
아래와 같은 일들이 가능하게 됩니다.
```
int num1 = 100;
int &num2 = num1;
int &num3 = num2;
int &num4 = num1;
```
또한 참조자는 반드시 변수에 대해서만 선언되어야 하고 선언됨과 동시에 무언가를 참조해야 합니다.

```
int &num = 10;(X)
int &num;(X)
int &num = NULL;(X)
```
포인터 변수도 변수이기 때문에 참조자의 선언이 가능합니다.

```
int num = 10;
int *ptr = &num;
int **dptr = &ptr;

int &ref = num;
int *(&pref) = ptr;
int **(&dpref) = dptr;
```

# 참조자 + 함수
C언어에서는 주로 call-by-reference를 하기 위해서 포인터를 이용했습니다.  
하지만 포인터를 이용한 call-by-reference는 명시적이지 않습니다.  
그 이유는 주소값을 받아서 단순히 주소 값을 증가시켜서 return해준다면 그것은 call-by-value가 되기 때문이지요.  
(참고 : call-by-reference란 함수 외부에 선언된 변수에 참조의 형태로 접근하여 제어하는 형태를 말합니다)  
예를들어 아래의 함수는 call-by-value입니다.
```
int * Example(int * ptr){
  return ptr+1;
}
```

하지만 참조자를 이용하면 해당 함수가 참조의 성격을 갖음을 명확히 파악할 수 있게 해줍니다.  
그리고 활용이 더 쉬운 것도 특징입니다.  
단점도 있는데 아래와 같은 코드에서 num이 몇이 출력될지 알 수 없습니다.  
C언어라면 100% 결과가 24라고 장담할 수 있지만 c++에서는 참조자로 인해 장담할 수 없게 되는 것입니다.  
이는 코드의 가독성을 해칩니다.

```
void Example(int &ref){
  내용내용내용
}
int num=24;
Example(num);
cout << num << endl;
```

하지만 이 단점도 아래와 같은 방법으로 어느정도 커버가 됩니다.
```
void Example(const int &ref){
  내용내용내용
}
int num=24;
Example(num);
cout << num << endl;
```
위에서 const int &ref는 함수 안에서 참조자 ref를 이용한 값의 변경은 하지 않겠다는 것을 나타냅니다.  
만일 변경한다면 컴파일 오류가 납니다.  

또한 반환형이 참조형이 될 수도 있습니다.  
```
int& Example(int &ref){
  ref++;
  return ref;
}
```

주로 반환값을 참조형 변수에 넣을 때 쓰입니다.  
반환형이 참조형이 아니라면 안되는 경우인데 예제를 통해 확인해 보겠습니다.  
```
int Example(int &ref){
  ref++;
  return ref;
}

int main(void){
  int num = 10;
  int &reff = Example(num);
}
```

위의 코드는 오류가 납니다.  
Example 함수의 리턴값은 상수나 다름없기 때문입니다.  
참조자는 반드시 변수에 대해서 선언되어야만 하기 때문에 오류가 나는 것입니다.  
따라서 아래와 같이 작성되어야합니다.  

```
int& Example(int &ref){
  ref++;
  return ref;
}

int main(void){
  int num = 10;
  int &reff = Example(num);
}
```

그리고 아래와 같은 참조자를 반환하는 함수를 만들지 않도록 주의해야 합니다.  

```
int& Example(int ref){
  ref++;
  return ref;
}

int main(void){
  int num = 10;
  int &reff = Example(num);
}
```
지역변수 ref를 참조의 형태로 반환해봤자 함수가 반환되면 ref는 소멸이 되기 때문에 지역변수를 참조형으로 반환하는 일은 없어야 합니다.  

그리고 또 큰 특징이 const 참조자입니다.  
앞서 상수를 참조할 수는 없다고 했지만 const를 이용하면 가능합니다.

```
const int &ref = 50;
```

이것이 가능한 이유는 const 참조자를 이용해서 상수를 참조하면 '임시변수'라는 것을 만들어서 이 장소에 상수 30을 저장하고선 참조자가 이를 참조하게끔 하기 때문입니다.  
이런 것이 가능하게 한 이유는 확장성 때문입니다.
```
int Adder(const int &num1, const int &num2){
  return num1 + num2;
}
```
위의 코드는 인자로 변수와 상수를 모두 받을 수 있다는 장점을 갖게됩니다.  

# 참조자 + 동적할당

C언어에서는 동적할당을 받으면 반드시 포인터를 이용해서만 접근이 가능했습니다.  
하지만 C++에서는 참조자를 이용해서 쉽게 접근이 가능합니다.  

```
int * ptr = new int;
int &ref = *ptr;
ref = 20;
cout << *ptr << endl;
결과 : 20
```

# const 참조자 + const 함수

const참조자를 이용해서는 const함수만 호출이 가능합니다.  
C++에서는 const 참조자를 대상으로 값의 변경 능력을 가진 함수의 호출을 허용하지 않기 때문입니다.  
즉 아래의 코드는 에러가 발생합니다.

```
class EasyClass{
private:
    int num;
public:
    void InitNum(int n){
        num = n;
    }
    int GetNum(){
        return num;
    }
};

class LiveClass{
private:
    int num;
public:
    void InitNum(const EasyClass &easy){
        num = easy.GetNum();
    }
};
```

에러를 없애려면 GetNum 함수에 const선언을 추가해야 합니다.
