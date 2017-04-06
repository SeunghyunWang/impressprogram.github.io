---
layout: post
comments: true
title: C언어 파일 입출력과 복사
categories: [c]
tags: [c, programming-language]
fullview: true
---
# 파일 입출력하기

FILE 구조체의 포인터를 선언해서 우리는 파일을 열 수 있습니다.  
```
FILE * fopen( const char *, const char * );
첫번째 인자 : 처리할 파일 명
두번째 인자 : 파일 처리 종류 지정 (모드)

예) FILE * fp = fopen("data.txt", "rt")
```
파일 처리 모드의 종류는 다음과 같이 있습니다.
```
r = 읽기 모드 / 파일이 없을 경우 에러 발생
w = 쓰기 모드 / 파일이 없을 경우 새로 만들고 파일이 존재하면 내용을 삭제하고 처음부터 기록
a = 추가 쓰기 모드 / 파일이 없을 경우 새로 만들고 파일이 존재하면 뒤에부터 이어서 기록
```
fp변수를 활용해서 그냥 파일을 입출력을 할수도 있습니다.  

```
int fputc(int c, FILE * stream); //문자 출력
int fgetc(FILE * stream); //문자 입력
int fputs(const char * s, FILE * stream); //문자열 출력
char * fgets(char * s, int n, FILE * stream); //문자열 입력
```
다음은 파일을 생성해서 문자열을 저장하는 예제입니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    FILE * fp = fopen("input.txt","wb");
    if(fp==NULL){
        puts("파일오픈 실패!");
        return -1;
    }

    fputc('A', fp);
    fputc('B', fp);
    fputs("My name is Hong \n", fp);
    fputs("Your name is Yoon \n", fp);
    fclose(fp);

    return 0;
}
```
이렇게 하면 다음의 텍스트파일이 생깁니다.
```
ABMy name is Hong
Your name is Yoon
```
이번에는 텍스트파일을 불러와서 출력하는 코드를 예제를 보겠습니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    char str[30];
    int ch;
    FILE * fp=fopen("input.txt","rt");

    if(fp==NULL){
        puts("파일 오픈 실패!");
        return -1;
    }

    ch = fgetc(fp);
    printf("%c \n", ch);
    ch = fgetc(fp);
    printf("%c \n", ch);

    fgets(str, sizeof(str),fp);
    printf("%s", str);

    fgets(str, sizeof(str),fp);
    printf("%s", str);

    fclose(fp);

    return 0;
}
```
아래는 결과값 입니다.  
```
A
B
My name is Hong
Your name is Yoon
```

# 파일 복사하기

파일을 복사하려면 파일의 끝을 확인해야 할 것입니다.  
```
int feof(FILE * stream);
-> 파일의 끝에 도달한 경우 0이 아닌 값 반환
```
더 이상 읽을 데이터가 존재하지 않으면 0이 아닌 값을 반환합니다.  
파일 복사와 같이 파일의 끝을 확인해야 하는 경우에 유용하게 사용됩니다.  
참고로 EOF의 경우에는 무조건 파일의 끝에 도달했다고 반환되는 것이 아니라 오류가 난 경우에도 반환이 되니 이것으로 파일의 끝에 도달했는지를 확인할 수는 없습니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    FILE * input=fopen("input.txt", "rt");
    FILE * des=fopen("dst.txt","rt");
    int ch;

    if(input==NULL || des==NULL){
        puts("파일오픈 실패!");
        return -1;
    }

    while((ch=fgetc(input))!=EOF)
        fputc(ch, des);

    if(feof(input)!=0)
        puts("파일복사 완료!");

    else
        puts("파일복사 실패!");

    fclose(input);
    fclose(des);

    return 0;
}
```

# 바이너리 파일 입출력(복사)

바이너리 파일이란 텍스트 파일을 제외하면 나머지는 다 바이너리 파일이라고 보셔도 됩니다.  
숫자로 된 파일이지요(사진같은 것도 알고보면 숫자로 이루어진 바이너리 파일입니다).  
바이너리 파일은 텍프트 파일보다 입출력이 훨씬 간단합니다.
```
int buf[12];
fread((void*)buf,sizeof(int),12,fp); //fp는 FILE 구조체 포인터
-> sizeof(int) 크기의 데이터 12개를 fp로부터 읽어들여서 배열 buf에 저장해라!

int buf[7] = {1, 2, 3, 4, 5, 6, 7};
fwrite((void*)buf, sizeof(int), 7, fp);
-> sizeof(int)크기의 데이터7개를 buf로부터 읽어서 fp에 저장해라!
```
자 그럼 이 두 함수를 이용해서 바이너리 파일을 복사하는 프로그램의 예제를 보겠습니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    FILE * src=fopen("profile.jpeg", "rt");
    FILE * des=fopen("pro.jpeg","wt");
    char buf[1000];
    int readCnt;

    if(src==NULL || des==NULL){
        puts("파일오픈 실패!");
        return -1;
    }

    while(1){
        readCnt = fread((void*)buf, 1, sizeof(buf), src);

        if(readCnt<sizeof(buf)){
            if(feof(src)!=0){
                fwrite((void*)buf,1,readCnt,des);
                puts("파일복사 완료");
                break;
            }
            else
                puts("파일복사 실패");

            break;
        }


    fwrite((void*)buf, 1, sizeof(buf), des);
    }

    fclose(src);
    fclose(des);
    return 0;
}
```
우선 readCnt = fread((void*)buf, 1, sizeof(buf), src); 구문을 통해서 src로부터 파일을 1*sizeof(buf) 만큼 buf로 읽어들입니다.  
if(readCnt<sizeof(buf)) 안에서는 buf사이즈보다 fread함수가 작은 값을 반환했을때 오류가 발생했거나 파일의 끝에 도달할 상태일 것입니다.  
if(feof(src)!=0)를 통해서 오류인지 파일의 끝에 도착한 것인지 체크해줍니다.  
그리고 위에서 배열 buf에 저장해 놓은 데이터를 그대로 des에 저장합니다.  
이걸 계속해서 반복하면? 파일이 전부 복사됩니다.  
사진같은걸로 해보세요!  
저는 제 프로필 사진으로 해보았는데 잘되네요 ㅎㅎ

# 텍스트와 바이너리 파일을 동시에 입출력하기

fprintf와 fscanf를 사용하면 쉽게 텍스트와 바이너리 파일을 동시에 입출력 할 수 있습니다.  
아래의 예제는 텍스트 데이터인 이름과 성별, 그리고 바이너리 데이터인 나이 정보를 하나의 파일에 저장하는 것입니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    char name[10];
    char sex;
    int age;

    FILE * fp = fopen("friend.txt", "wt");
    int i;

    for(i=0; i<3; i++){
        printf("이름 성별 나이 순 입력: ");
        scanf("%s %c %d", name, &sex, &age);
        getchar(); //scanf함수는 엔터 키의 입력을 읽지 않고 버퍼에 남겨두므로 엔터 키의 소멸을 위한 코드
        fprintf(fp, "%s %c %d", name, sex, age);
    }
    fclose(fp);
    return 0;
}
```
그리고 아래의 예제는 위의 파일을 읽어들이는 예제입니다.
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    char name[10];
    char sex;
    int age;

    FILE * fp = fopen("friend.txt", "wt");
    int ret;

    while(1){

        ret = fscanf(fp, "%s %c %d", name, &sex, &age);
        if(ret==EOF)
            break;
        printf( "%s %c %d", name, sex, age);
    }
    fclose(fp);
    return 0;
}
```  
**그렇다면 텍스트와 바이너리 데이터가 둘 다 있는 구조체 변수의 경우에는 어떻게 입출력을 할까요?**
구조체 변수를 하나의 바이너리 데이터로 인식하고 처리하면 됩니다.
```
#include <stdio.h>
typedef struct fren{
    char name[10];
    char sex;
    int age;
} Friend;

int main(int argc, const char * argv[]) {
    FILE * fp;

    Friend myfren1;
    Friend myfren2;

    //파일 입력하기
    fp = fopen("friend.bin","wb");
    printf("이름, 성별, 나이 순 입력: ");
    scanf("%s %c %d", myfren1.name, &(myfren1.sex), &(myfren1.age));
    fwrite((void*)&myfren1, sizeof(myfren1),1,fp);
    fclose(fp);

    //파일 출력하기
    fp=fopen("friend.bin","rb");
    fread((void*)&myfren2, sizeof(myfren2),1,fp);
    printf("%s %c %d \n", myfren2.name, myfren2.sex, myfren2.age);
    fclose(fp);

    return 0;
}
```

# 파일 위치 지시자

경우에 따라서는 파일의 중간 또는 마지막 부분에 저장된 데이터의 일부를 읽어야 하는 경우도 있을 것입니다.  
이럴 경우 파일 위치 지시자를 통해서 이동시킬 수 있습니다.  
![fseek함수](https://impressprogram.github.io/assets/media/fseek.png)  
기본적인 사용법은 int fseek(FILE * stream, 이동거리, 시작위치); 입니다.  
위의 그림에서 보듯이 이동거리에 양수가 들어가면 파일의 끝쪽으로 x칸 이동하고 음수가 들어가면 파일의 앞쪽으로 x칸 이동합니다.  
그리고 시작 위치에 SEEK_SET이 들어가면 파일의 맨 앞부터 이동하고 SEEK_CUR이면 현재 위치, SEEK_END이면 파일의 끝부터 이동을 시작합니다.  
다음은 예제입니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    FILE * fp = fopen("text.txt", "wt");
    fputs("123456789",fp);
    fclose(fp);

    fp=fopen("text.txt","rt");

    fseek(fp,-2,SEEK_END);
    putchar(fgetc(fp));

    fseek(fp, 2, SEEK_SET);
    putchar(fgetc(fp));

    fseek(fp, 2, SEEK_CUR);
    putchar(fgetc(fp));

    fclose(fp);

    return 0;
}
결과 : 836
```
맨 처음 파일의 끝(EOF)에서 -2칸 이동해서 8이 출력됩니다.  
그리고 맨 앞에서(1) 2칸을 이동해서 3이 출력됩니다.  
3이 출력되면서 4를 가리키게 되고 거기서 2칸을 더 이동해서 6이 출력됩니다.  
그렇다면 파일 위치 지시자의 위치 정보를 알려주는 함수도 있을까요?  
ftell이라는 함수가 있습니다.  
```
long ftell(FILE * stream);
-> 첫번째 바이트를 가리키면 0출력 세번째 바이트를 가리키면 2출력
```
이 ftell이라는 함수는 다음과 같이 파일 위치 지시자의 정보를 임시로 저장할 때에 유용하게 사용됩니다.  
```
#include <stdio.h>

int main(int argc, const char * argv[]) {
    long fpos;
    int i;

    FILE * fp=fopen("text.txt", "wt");
    fputs("1234-",fp);
    fclose(fp);

    fp=fopen("text.txt","rt");

    for(i=0; i<4; i++){
        putchar(fgetc(fp));
        fpos = ftell(fp);
        fseek(fp, -1, SEEK_END);
        putchar(fgetc(fp));
        fseek(fp, fpos, SEEK_SET);
    }
    fclose(fp);

    return 0;
}
결과 : 1-2-3-4-
```
코드를 조금 설명해 드리자면 파일 위치 지시자의 정보를 fpos에 저장하고 있는 것입니다.  
이로써 파일 위치 지시자를 어디로 이동시키건 다시 이전 위치로 되돌릴 수 있게 됩니다.
