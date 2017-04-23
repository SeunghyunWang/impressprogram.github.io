---
layout: post
comments: true
title: 소프트웨어 구현 방법론
categories: [datastructure]
tags: [datastructure]
fullview: true
---

# 소프트웨어의 생명주기

1. 요구사항(requirements) 분석  

- 문제에 대한 적절한 해를 구하기 위한 요구조건을 정의  
- 프로젝트들의 목적을 정의한 명세 (specification) 들의 집합  
- 대부분 슬랙이나 카카오톡 등 메신저로 의견을 나눈 뒤에 워드나 ppt로 문서화 함

2. 명세 (specification)

- 기능적 명세 (functional specification)
- 입력/출력 명세 – 구문적 제약조건 (비 절차적 명세)
- 기능적 명세 – 의미적 제약조건 (절차적 또는 비절차적)

3. 설계(design)

- 명세된 기능을 어떻게 달성하는가를 기술 (추상적 언어: Pseudo code)
- 전체 시스템 설계 : top-down, bottom-up
- 추상 자료형 (abstract data type) – 자료객체와 연산
- 자료 객체 (object)들과 수행될 연산 (operation) 의 정의

(예: 수강등록 시스템 – student, course, 등을 object 라고 하고, 이 object에 적용할 insert, remove 등이 operation 이다 )

4. 구현(implementation) – coding

- 구조화 프로그래밍 (assignment, conditional, loop)
- modular 프로그래밍 (function, entry/exit point)

5. 검증(Verification)

- 테스트(testing): 프로그램의 수행 검증, 프로그램 성능 검사 (모듈검사, 통합검사, 시스템 검사..)
- 정확성 증명(correctness proofs): 수학적 기법들을 사용하여 프로그램의 정확성 증명
- debugging - 오류제거

6. 운영 및 유지보수 (operation and maintenance)

- 시스템 설치(installation), 운영, 유지보수
- 새로운 요구조건 – 변경
- 수정 내용 기록 유지

# 소프트웨어 설계 방법론

1. 하향식(top-down): 크고 복잡한 시스템 개발시 사용

- 먼저 문제를 정의합니다.
- 그 문제를 해결하기 위한 세부 작업을 정의합니다.
- 또 그 세부 작업을 해결하기 위한 세부 작업을 정의합니다.
- 이런 방식으로 쭈욱 내려갑니다.

2. 상향식(bottom-up): 하위레벨 부터 상세히 프로그램을 작성

- Module 개발(하나의 기능수행)
- Module 통합 - 전체 시스템이 완성됨
- 이렇게 저수준의 프로그래밍을 먼저 한 뒤에 고수준의 프로그래밍을 완성시키는 것

# 데이터 추상화 와 캡슐화

캡슐화 : 관련있는 데이터와 함수를 묶고 실제 구현 내용 일부를 외부에 감추어 은닉한다. 간접적 접근경로는 제공해야함.  

```
class Point{
private:
    int x;
    int y;
public:
    int GetX() {return x;}
    int GetY() {return y;}
    void SetX(int _x);
    void SetY(int _y);
    void Showdata();
};

void Point::Showdata() {//내용}

int main() {
    p.SetX(x);
    p.SetY(y);
    p.Showdata();
}
```

위의 코드는 점에 대한 정보를 클래스에서 잘 묶어놓고 있습니다.  
그리고 실질적인 데이터에 대한 간접적 접근경로도 제공하고 있으므로 캡슐화되었다고 볼 수 있습니다.  

추상화 : 무엇과 어떻게를 구분하는 것. 데이터의 명세와 구현을 분리함.  

위의 예제에서 x와 y는 데이터 즉 무엇이고, public에 있는 함수들은 기능 어떻게에 해당합니다.  

**추상화 와 캡슐화의 장점**  

1. 소프트웨어 개발의 간소화: 복잡한 작업을 부분작업들로 분해 (분업 가능)  
2. 검사와 디버깅의 단순화: 부분 작업  
3. 재 사용성: 자료 구조가 시스템에서 별개의 개체로 구현  

# 알고리즘

특정한 일을 수행하기 위한 명령어의 유한 집합으로 동일한 문제에 여러 개의 알고리즘이 존재합니다.  
주로 의사코드로 작성을 한 뒤에 실제 코드로 구현합니다.  

**의사코드 작성 요령**  

![pseudocode1](https://impressprogram.github.io/assets/media/pseudocode1.png)  
![pseudocode2](https://impressprogram.github.io/assets/media/pseudocode2.png)