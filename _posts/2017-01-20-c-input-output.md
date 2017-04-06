---
layout: post
comments: true
title: C언어 스트림이란? & 문자 출력함수와 입력함수
categories: [c]
tags: [c, programming-language]
fullview: true
---
# 스트림이란
우리가 프로그램을 하나 작성했다고 가정합시다.  
이 프로그램을 작성하기 위해서는 키보드로 타이핑을 했을 것입니다.  
그리고 프로그램을 모니터를 통해 봅니다.  
그런데 생각해보아야 할 것은 프로그램과 키보드, 모니터는 각각 서로 떨어져있는 개체입니다.  
어떻게 이들이 연결될 수 있을까요?  
프로그램상에서 모니터와 키보드를 대상으로 데이터를 입출력 하기 위해서는 이들을 연결시켜 주는 다리가 필요할 것입니다.  
이러한 다리의 역할을 하는 매개체를 가리켜 '스트림(stream)'이라고 합니다.  
스트림의 내부에는 입출력 버퍼가 있는데 이 버퍼는 스트림에 의해 관리되므로 우리는 신경쓸 것이 없습니다.  
스트림에 관련한 내용의 아래의 블로그에서 재미있게 그림을 곁들여 설명해 주고 있습니다.  
http://blog.naver.com/PostView.nhn?blogId=skout123&logNo=50132640905&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView  
우리는 표준 입출력 함수들을 이용해서 쉽게 데이터를 입출력 할 수 있습니다.  
입출력이 이루어지는 자세한 과정은 우선 표준 입출력 함수들을 살펴본 뒤에 살펴보도록 하겠습니다.  

# 문자단위 입출력함수
자, 그럼 우리는 문자 출력함수와 입력함수를 보도록 하겠습니다.  
```
문자 단위 출력 함수
int putchar(int c);
int fputc(int c, FILE * stream);
-> 함수 호출 성공 시 쓰여진 문자가, 실패 시 EOF반환

 문자 단위 입력 함수
int getchar(void);
int fgetc(FILE * stream);
-> 파일의 끝에 도달하거나 함수호출 실패 시 EOF 반환
```
차이점은 putchar나 getchar는 stdout, stdin으로 표현되는 표준 입력 스트림으로부터 하나의 문자를 입력받거나 전송하는 함수 입니다.  
그에 반해 fputc나 fgetc는 문자를 전송할 스트림을 지정할 수 있어서 stdout이나 stdin 뿐만 아니라 파일을 대상으로도 데이터를 입력받거나 전송할 수 있습니다.  

위에서 EOF를 이야기 하였는데 아래의 예제를 통해서 이것도 살펴보도록 하겠습니다.  
```
#include <stdio.h>

int main(void){
  int ch;

  while(1){
    ch = getchar();
    if(ch==EOF)
      break;
    putchar(ch);
  }
  return 0;
}
```
위와 같이 짜면 계속해서 문자를 하나 입력받으면 바로 그 문자를 리턴해 줍니다.  
종료를 하려면 윈도우는 CTRL + Z를, 리눅스는 CTRL + D키를 눌러야 합니다.  
이것을 눌러야 EOF가 반환되도록 약속해 놓은 것입니다.

# 문자열 단위 입출력 함수
흔히 쓰이는 scanf는 공백을 입력받지 못하지만 공백을 입력받는 방법이 있습니다.  
아래에서 소개해 드리는 gets 나 fgets를 사용하시면 됩니다.  
```
문자열 출력 함수
int puts(const char * s);
int fputs(const char * s, FILE * stream);
-> 성공 시 음수가 아닌 값을, 실패 시 EOF 반환

문자열 입력함수
char * gets(char *s);
char * fgets(char * s, int n, FILE * stream);
파일의 끝에 도달하거나 함수호출 실패 시 NULL 포인터 반환
```

문자열 출력함수의 사용 예시입니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    char * str = "Simple String";

    printf("hi \n");
    puts(str);
    puts("-----------");
    fputs(str,stdout);printf("\n");
    fputs("hi~",stdout);printf("\n");

    return 0;
}
결과값
hi
Simple String
-----------
Simple String
hi~
```

문자열 입력함수의 사용 예시입니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {

    char str[7];
    int i;

    for(i=0; i<3; i++){
        fgets(str,sizeof(str), stdin);
        printf("Read %d : %s \n",i+1,str);
    }
    return 0;
}
```
My name is myeongsoo라고 입력했을 때의 결과입니다.  
```
Read 1 : My nam
Read 2 : e is m
Read 3 : yeongs
```
공백을 신경쓰지 않고 '\0'를 포함해 7글자를 읽습니다.  
엔터키까지도 입력으로 받아들이니 주의하셔야합니다.  

# 표준 입출력과 버퍼
앞서 우리가 공부한 것들이 ANSI C의 표준에서 정의된 '표준 입출력 함수'입니다.  
이러한 표준 입출력 함수를 통해서 데이터를 입출력하는 경우, 해당 데이터들은 운영체제가 제공하는 '메모리 버퍼'를 중간에 통과하게 됩니다.  
여기서 말하는 메모리 버퍼는 데이터를 임시로 모아두는 곳인데요!  
즉, 우리가 키보드를 통해 입력하면 입력버퍼에 우선 저장되는 것입니다.  
키보드로부터 입력된 데이터가 입력 스트림을 거쳐서 입력 버퍼로 들어가는 시점은 엔터키가 눌리는 시점입니다.  
그렇다면 데이터를 목적지로 바로 전송하지 않고 중간에 입출력 버퍼를 두는 이유는 무엇일까요?  
데이터 전송의 효율성 때문입니다.  
바로바로 이동시키는 것보다 메모리 버퍼를 둬서 데이터를 한데 묶어서 이동시키는 것이 효율이 좋고 빠릅니다.  
그림과 함께 보고 싶으신 분들은 아래의 블로그에서 보는 것을 추천드립니다.  
http://mintnlatte.tistory.com/411  

# 버퍼 비우기
저 블로그에서 보시면 fflush라는 함수가 보일텐데 저것은 잘 쓰이는 것이 아니니 넘어가셔도 됩니다.  
fflush는 버퍼를 비워주는 함수이지만 잘 쓰이지 않고 대신 아래의 방법이 잘 쓰입니다.  
```
#include <stdio.h>
void ClearLineFreomReadBuffer(void){
    while(getchar() != '\n');
}

int main(int argc, const char * argv[]) {

    char perID[7];
    char name[10];

    fputs("주민번호 앞 6자리 입력: ", stdout);
    fgets(perID, sizeof(perID), stdin);
    ClearLineFreomReadBuffer();

    fputs("이름 입력: ", stdout);
    fgets(name, sizeof(name), stdin);

    printf("주민번호: %s\n", perID);
    printf("이름 : %s\n", name);

    return 0;
}
```
위의 코드에서 ClearLineFreomReadBuffer 함수가 갖는 의미를 살펴봅시다.  
만일 저 함수가 없다면 주민번호 6자리를 입력하고 엔터를 치면 엔터와 문자열의 끝에 자동으로 붙는 '\0'가 붙어서 perID에는 주민번호 6자리와 '\0'가 입력됩니다.  
그렇다면 버퍼에는 아직 엔터가 남아있죠?  
그것이 다음 name으로 넘어가서 우리는 이름을 치지도 않았는데 입력은 자동으로 엔터키로 입력되어서 넘어가버리고 맙니다.  
```
ClearLineFreomReadBuffer가 없다면...
주민번호 앞 6자리 입력: 930809
이름 입력: 주민번호: 930809
이름 :
```
위의 같은 결과가 나와버리고 마는 것입니다.  
엔터를 쳤을 때까지 문자를 읽어주는 ClearLineFreomReadBuffer 함수를 정의함으로써 우리는 입력버퍼에서 엔터를 포함한 모든 문자를 지워줄 수 있습니다.  
이번 포스팅이 이해가 안가시면 다음 포스팅까지 보시는 것을 추천드립니다.  
