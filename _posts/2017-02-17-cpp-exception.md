---
layout: post
comments: true
title: C++ 예외처리에 대하여
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 예외 처리 기본

예외 처리는 어떨때 쓰일까요?  
예를 들어서 정수를 두개 입력받아 나누는 프로그램을 짠다고 가정하겠습니다.  

```
#include <iostream>

using namespace std;

int main(){
    int num1, num2;
    cout << "두 개의 숫자 입력 : ";
    cin >> num1 >> num2;

    if(num2==0)
        cout << "제수는 0이 될 수 없습니다. 프로그램을 다시 시작해주세요" << endl;
    else{
        cout << "몫 : " << num1/num2;
        cout << "나머지 : " <<num1%num2 << endl;
    }

    return 0;
}
```

위의 코드는 제수가 0일 경우에 예외를 처리해준 코드입니다.  
그런데 if문을 예외처리로 쓴다면 사람들이 이것이 조건문인지 예외처리구문인지 명확히 구분하지 못 할 것입니다.  
이는 try, catch, throw를 쓴다면 쉽게 해결할 수 있습니다.  

try{} 블록은 검사의 범위를 지정할 때 사용됩니다.  
즉, 예외발생 예상되는 코드를 넣는 곳입니다.  
여기서 예외가 발생한다면 catch로 보냅니다.  
catch{} 블록은 예외를 처리하는 코드를 담습니다.  
try 블록 내에서 발생하는 예외는 이어서 등장하는 catch 블록에서 처리되는 것입니다.  
throw는 예외가 발생했음을 알립니다.  
즉, throw에 의해 던져진 예외 데이터는 throw를 감싸는 try 블록에 의해 감지가 되어서 이어서 등장하는 catch 블록에 의해 처리되는 것입니다.  

```
#include <iostream>

using namespace std;

int main(){
    int num1, num2;
    cout << "두 개의 숫자 입력 : ";
    cin >> num1 >> num2;

    try{
        if(num2==0)
            throw num2;
        cout << "몫 : " << num1/num2 << endl;
        cout << "나머지 : " <<num1%num2 << endl;
    }
    catch(int expn){
        cout << "제수는 " << expn <<"이 될 수 없습니다." << endl;
        cout << "프로그램을 다시 실행하세요." << endl;
    }

    return 0;
}
```

위의 코드가 맨 처음에 if문으로 먼저 소개해드렸던 예외처리를 try catch 구문으로 바꾼 것입니다.  
그리고 catch 인자의 자료형은 throw의 자료형에 영향을 받습니다.  
즉, int형을 throw하고 char형을 throw하면 catch문을 2개를 만들어서 하나는 int형을 받아서 예외처리하고 하나는 char형을 받아서 예외처리를 할 수가 있습니다.  

# 스택풀기

어떤 함수에서 throw를 해준다면 어떤 방식으로 catch를 해줄까요?  
뒤의 예제로 설명해 보겠습니다.  

```
void f1(){
    f2();
}
void f2(){
    f3();
}
void f3(){
    throw exception;
}
void main(){
    try{
        f1();
    }
    catch(인자){
        내용
    }
}
```

f(1)-> f(2) -> f(3)이 실행되고 exception을 throw합니다.  
그렇다면 f(3)에 catch가 없는 것을 확인하고 f(2)로 throw를 전달, 마찬가지로 f(1)로 전달, main으로 전달한 뒤에 main에 있는 catch문에 인자를 전달해줍니다.  
이런 예외 데이터의 전달을 가리켜, 스택풀기(스택의 반환)라고 합니다.  

# 전달되는 예외의 특징

함수 내에서 전달될 수 있는 에외의 종류와 그 상황을 명시할 수 있습니다.  

```
int Simple(int num) throw (int,char){내용}
```

위의 코드는 Simple 함수 내에서 예외상황의 발생으로 인해서 int형 예외 데이터와 char형 예외 데이터가 전달될 수 있음을 나타내는 것입니다.  
따라서 프로그래머는 아래와 같이 이 함수를 호출하게 될 것입니다.  

```
try{
  Simple(20);
}
catch(int x){내용}
catch(char x){내용}
```

또한 모든 예외를 다 받아주겠다고 표시할 수도 있습니다.

```
catch(...) //이건 전달되는 모든 예외를 다 받아주겠다는 선언입니다.
```

추가로 catch구문 안에서 throw를 할 수도 있습니다.

```
#include <iostream>

using namespace std;

void Divide(int num1, int num2){
    try {
        if(num2 ==0)
            throw 0;
        cout << "몫 : " << num1/num2 << endl;
        cout << "나머지 : " << num1%num2 << endl;
     } catch (int expn) {
         cout << "first catch" << endl;
         throw; //다시 예외를 던짐
    }
}

int main(){
    try {
        Divide(9, 2);
        Divide(4, 0);
    } catch (int expn) {
        cout << "second catch" << endl;
    }

    return 0;
}
결과:
몫 : 4
나머지 : 1
first catch
second catch
```

# 예외 클래스와 예외 객체  

기본 자료형 뿐만 아니라 클래스의 객체도 예외 데이터가 될 수 있으며 이것이 일반적인 방법입니다.  
다음과 같이 상속관계로 묶어서 예외의 처리를 단순화 할 수 있습니다.  

```
#include <iostream>
#include <cstring>

using namespace std;

class AccountException{
public:
    virtual void ShowExceptionReason()=0;
};

class DepositException : public AccountException{
private:
    int reqDep;
public:
    DepositException(int money) : reqDep(money){}
    void ShowExceptionReason(){
        cout <<"예외 메시지 : " << reqDep << "는 입금불가" << endl;
    }
};

class WithdrawException : public AccountException{
private:
    int balance;
public:
    WithdrawException(int money) : balance(money){}
    void ShowExceptionReason(){
        cout << "예외 메세지 : " << balance << ", 잔액부족" << endl;
    }
};

class Account{
private:
    char accNum[50];
    int balance;
public:
    Account(char * acc, int money) : balance(money){
        strcpy(accNum, acc);
    }
    void Deposit(int money) throw (AccountException){
        if(money<0){
            DepositException expn(money);
            throw expn;
        }
        balance += money;
    }
    void Withdraw(int money) throw (AccountException){
        if(money>balance)
            throw WithdrawException(balance);
        balance -= money;
    }
    void ShowMyMoney(){
        cout << "잔고 : " << balance << endl << endl;
    }
};

int main(){
    Account myAcc("56789-987654", 5000);

    try{
        myAcc.Deposit(2000);
        myAcc.Deposit(-300);
    }
    catch(AccountException &expn){
        expn.ShowExceptionReason();
    }
    myAcc.ShowMyMoney();

    try {
        myAcc.Withdraw(3500);
        myAcc.Withdraw(4500);
    } catch (AccountException &expn) {
        expn.ShowExceptionReason();
    }
    myAcc.ShowMyMoney();

    return 0;
}
```

DepositException과 WithdrawException는 AccountException을 상속받습니다.  
따라서 try catch 구문에서 AccountException 참조 자료형으로 받아서 쉽게 예외처리를 해줄 수 있습니다.  
인자가 AccountException 참조 자료형이긴 하지만 실제로 가리키는 값은 자식 클래스라면 전달되는 인자에 따라 결정되기 때문에 위와 같이 유연하게 처리해 줄 수 있는 것입니다.  
