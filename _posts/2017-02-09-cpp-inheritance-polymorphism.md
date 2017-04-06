---
layout: post
comments: true
title: C++ 상속과 다형성 + 가상함수
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 상속의 개념

매우매우매우매우 중요한 객체지향의 개념중 하나입니다.  
상속을 하면 연관된 일련의 클래스에 대해 공통적인 규약을 정의할 수 있습니다.  
보통 IS-A 개념으로 많이들 설명 하십니다.  
이게 어떤 것이냐구요? 예를 들어 보겠습니다.  
사람 클래스를 하나 정의하겠습니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char * name;
    int age;
public:
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

int main()
{
    Person student("김명수", 25);

    student.Walk();
    student.Info();
}
```

그런데 사람을 더 다양한 종류의 클래스로 나누고 싶어 졌습니다.  
학생, 선생님 등등 다양한 클래스를 추가해 나가려고 할 때에 일일히 클래스를 각자 다 만들어줘야 할까요?  
이럴때 상속이라는 개념을 씁니다.  
상속은 함부로 쓰지 말고 IS - A, 즉 학생 is a 사람, 선생님 is a 사람 관계가 될 때 써야 합니다.  
왜냐구요? 확장성면에서 좋거든요(아무거에나 상속을 쓴다면 프로그램이 나중에 더 복잡해집니다).  
그럼 학생을 사람 클래스로부터 상속받아서 쉽게 구현해 보겠습니다.

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
public:
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
public:
    Student(char * myname, int myage, int mygrade) : Person(myname, myage), grade(mygrade){}

    void Study(){
        cout << "공부하는 중입니다." << endl;
    }
};

int main()
{
    Student student("김명수", 25,3);

    student.Walk();
    student.Study();
    student.Info();
}
결과:
걷는중입니다.
공부하는 중입니다.
이름 : 김명수
나이 : 25
```

Student 클래스에서 정의하지 않은 Person 클래스에 대한 내용도 사용할 수 있습니다.  
주의할 사항으로는 Student 클래스는 Person 클래스의 멤버까지 초기화를 해줘야 합니다.  
그리고 왠만하면 초기화를 할 때에 Person 클래스의 생성자를 호출해서 멤버를 초기화 하는 것이 좋습니다.  
그리고 여기서 Student를 자식클래스, 혹은 유도클래스, Person을 부모클래스, 혹은 기초클래스 라고 합니다.  

자 그리고 위에서 모르는 문법이 하나 있을 것입니다.  
class Student : public Person 에서 public 이라는 말은 왜 붙은 것일까요?  
저 자리에는 public, protected, private이 올 수 있습니다.  
먼저 설명을 드리자면 protected는 조금 생소할 수 있는데 자식의 클래스에서도 접근할 수 있도록 하게 해주는 것입니다.  
아래의 예제를 보면 이해가 갈 것입니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
protected:
    int hour;
public:
    Person(char * myname, int myage, int myhour) : age(myage), hour(myhour){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
public:
    Student(char * myname, int myage, int mygrade, int myhour) : Person(myname, myage, myhour), grade(mygrade){}

    void Study(){
        cout << hour << "시간 째 공부하는 중입니다." << endl;
    }
};

int main()
{
    Student student("김명수", 25,3,1);

    student.Walk();
    student.Study();
    student.Info();
}
```

좀 전의 예제를 조금만 바꾸었습니다.  
상속을 받았는데 Student에서 Person클래스에서 protected로 정의된 hour에 접근할 수 있다는 것을 알 수 있습니다(void Study()함수).  
private과 같은 효과가 있지만 상속된 클래스에서도 접근할 수 있다는 차이점이 있습니다.  
본론으로 돌아와서 class Student : public Person 에서 public 이라는 말은 왜 붙은 것일까요?  
public이 갖는 의미는 public보다 접근의 범위가 넓은 멤버는 public으로 변경시켜서 상속하겠다는 의미입니다.  
즉 아래와 같이 표현됩니다.  

```
public 상속
부모 클래스의 private -> 접근불가
부모 클래스의 protected -> protected
부모 클래스의 public -> public

protected 상속
부모 클래스의 private -> 접근불가
부모 클래스의 protected -> protected
부모 클래스의 public -> protected

private 상속
부모 클래스의 private -> 접근불가
부모 클래스의 protected -> private
부모 클래스의 public -> private
```

상속의 종류가 이렇게 있기는 하지만 public 상속이 대부분입니다.  

# 객체 포인터의 참조관계

객체 포인터는 위의 예제에서 Person 객체 뿐만 아니라 이를 상속하는 Student 객체까지 가리킬 수 있습니다.  
**참고로 참조자의 참조관계도 같으니 같이 생각해주셔도 됩니다.**  

```
Person * ptr1 = new Person;
Person * ptr2 = new Student;
```

그렇다면 ptr1과 ptr2는 각각 Person, Student의 객체에 접근할 수 있을까요?  
아닙니다.  
C++에서는 둘 다 Person을 자료형으로 인식해서 둘 다 Person 객체에만 접근할 수 있습니다.  
즉, 아래의 코드는 오류가 납니다.  

```
ptr2 -> Study(); //오류
```

이래서 나온 것이 바로 가상함수입니다.  
이를 이용한다면 위와 같은 문제를 해결할 수 있습니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
public:
    Person(){
        strcpy(name, "김명수");
        age = 25;
    }
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    virtual void Study(){

    }

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
    int hour;
public:
    Student() : Person(){
        hour = 10;
    }
    Student(char * myname, int myage, int mygrade, int myhour) : Person(myname, myage), grade(mygrade), hour(myhour){}

    void Study(){
        cout << hour << "시간 째 공부하는 중입니다." << endl;
    }
};

int main()
{
    Person * ptr1 = new Person;
    Person * ptr2 = new Student;

    ptr1->Info();
    ptr2->Study();
}
```

부모 클래스에 virtual void Study(){} 코드를 추가했습니다.  
virtual 을 함수 앞에 붙여주면 가상 함수 선언이 됩니다.  
앞으로 자식 클래스에 해당 함수의 이름이 있다면 위와 같은 상황에서 해당 함수 호출 시, 포인터의 자료형을 기반으로 호출대상을 결정하지 않고, 포인터 변수가 실제로 가리키는 객체를 참조하여 호출의 대상을 결정합니다.  

# 순수 가상함수

위의 Person 클래스를 보면 이는 학생, 선생님 등등이 상속할 부모 클래스 역할을 하지만 실제로 객체 생성을 목적으로 정의한 클래스는 아니라고 가정해봅시다.   
이 경우에 실수로라도 Person 클래스로 객체를 생성할 수 있기 때문에 객체의 생성을 문법적으로 막아주는 것이 좋습니다.  
순수 가상함수란 함수의 몸체가 정의되지 않은 함수를 의미합니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
public:
    Person(){
        strcpy(name, "김명수");
        age = 25;
    }
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    virtual void Study() const = 0;

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
    int hour;
public:
    Student() : Person(){
        hour = 10;
    }
    Student(char * myname, int myage, int mygrade, int myhour) : Person(myname, myage), grade(mygrade), hour(myhour){}

    void Study() const{
        cout << hour << "시간 째 공부하는 중입니다." << endl;
    }
};

int main()
{
    Person * ptr1 = new Person; //에러
    Person * ptr2 = new Student; //정상(?)
    Student * ptr3 = new Student; //정상

    ptr1->Info();
    ptr2->Study();
}
```

기존의 코드와 차이점은 virtual void Study() const = 0; 부분 입니다.  
0의 대입을 의미하는 것은 아니고 명시적으로 몸체를 정의하지 않았음을 컴파일러에게 알리는 것입니다.  
저 순수 가상함수 덕분에 위의 코드는 Person 자료형으로 객체를 생성하려 했지만 에러가 나게 됩니다.  
에러의 원인은 하나 이상의 멤버함수를 순수 가상함수로 선언했기 때문입니다.  
이렇게 되면 Person 객체는 추상 클래스가 되어서 불완전하게 되어 객체 생성이 불가능해집니다(상속은 가능).  
추가로 잘 보시면 'Person * ptr2 = new Student; //정상(?)' 라고 표시해두었습니다.  
그 이유는 가상소멸자의 부재 때문입니다.  

# 가상 소멸자

가상 소멸자가 무엇이길래 정상 출력되는 위의 예제에 정상(?)라고 표시했을까요?  
아래의 예제로 살펴보겠습니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
public:
    Person(){
        strcpy(name, "김명수");
        age = 25;
        cout << "사람 객체 생성" << endl;
    }
    ~Person();
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    virtual void Study() const = 0;

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
    int hour;
public:
    Student() : Person(){
        hour = 10;
        cout << "학생 객체 생성" << endl;
    }
    ~Student();
    Student(char * myname, int myage, int mygrade, int myhour) : Person(myname, myage), grade(mygrade), hour(myhour){}

    void Study() const{
        cout << hour << "시간 째 공부하는 중입니다." << endl;
    }
};

Person::~Person(){
    cout << "사람 객체 소멸" << endl;
}

Student::~Student(){
    cout << "학생 객체 소멸" << endl;
}

int main()
{
    Person * ptr2 = new Student; //정상(?)
    delete ptr2;
}
결과:
사람 객체 생성
학생 객체 생성
사람 객체 소멸
```

위의 예제에 생성자와 소멸자에 텍스트가 출력되도록 추가했습니다.  
보시다시피 사람 객체와 학생 객체가 생성되고 둘다 소멸되어야 하는데 사람 객체만 소멸되고 있습니다.  
바로 가상 소멸자가 없기 때문입니다.  

```
#include<iostream>

using namespace std;

class Person{
private:
    char name[50];
    int age;
public:
    Person(){
        strcpy(name, "김명수");
        age = 25;
        cout << "사람 객체 생성" << endl;
    }
    virtual ~Person();
    Person(char * myname, int myage) : age(myage){
        strcpy(name,myname);
    }

    void Walk(){
        cout << "걷는중입니다." << endl;
    }

    virtual void Study() const = 0;

    void Info(){
        cout << "이름 : " << name << endl;
        cout << "나이 : " << age << endl;
    }
};

class Student : public Person{
private:
    int grade;
    int hour;
public:
    Student() : Person(){
        hour = 10;
        cout << "학생 객체 생성" << endl;
    }
    ~Student();
    Student(char * myname, int myage, int mygrade, int myhour) : Person(myname, myage), grade(mygrade), hour(myhour){}

    void Study() const{
        cout << hour << "시간 째 공부하는 중입니다." << endl;
    }
};

Person::~Person(){
    cout << "사람 객체 소멸" << endl;
}

Student::~Student(){
    cout << "학생 객체 소멸" << endl;
}

int main()
{
    Person * ptr2 = new Student; //비정상
    delete ptr2;
}
결과:
사람 객체 생성
학생 객체 생성
학생 객체 소멸
사람 객체 소멸
```
이제는 정상적으로 객체가 소멸하고 있음을 알 수 있습니다.  

# 다형성

다형성은 사실 여지껏 다룬 내용입니다.  
다형성이란 모습은 같은데 형태는 다르다라는 말입니다.  
이를 C++에 적용하면 문장은 같은데 결과는 다르다는 말입니다.  
아래의 예제를 보면 이해가 갈 것입니다.  

```
#include<iostream>

using namespace std;

class Person{
private:

public:


    virtual void Walk(){
        cout << "걷는중입니다." << endl;
    }

};

class Student : public Person{

public:

    void Walk(){
        cout << "뛰는 중입니다." << endl;
    }
};

int main()
{
    Person * ptr = new Person();
    ptr -> Walk();
    delete ptr;

    ptr = new Student();
    ptr -> Walk();
    delete ptr;

    return 0;
}
결과 :
걷는중입니다.
뛰는중입니다.
```
위가 C++에서의 다형성의 예시입니다.
