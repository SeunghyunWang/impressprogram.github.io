---
layout: post
comments: true
title: 문자열 토크나이징에 대한 이야기(c)
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

(부경 대학교 권오흠 교수님의 인강을 듣고 작성하는 포스팅)  
# 문자열 토크나이징이란
우선 아래의 코드를 같이 살펴보면서 문제점도 같이 파악해보겠습니다.  
전화번호를 저장하고 출력하는 프로그램입니다.  

```
#include <stdio.h>
#include <string.h>

#define CAPACITY 100
#define BUFFER_SIZE 20

char * names[CAPACITY];
char * numbers[CAPACITY];
int n=0;

void add();
void find();
void status();
void delete();
void load();
void save();

int main(){
    char command[BUFFER_SIZE];

    while(1){
        printf("$ ");
        scanf("%s", command);

        if(strcmp(command, "add")==0)
            add();
        else if(strcmp(command, "find")==0)
            find();
        else if(strcmp(command, "status")==0)
            status();
        else if(strcmp(command, "delete")==0)
            delete();
        else if(strcmp(command, "load")==0)
            load();
        else if(strcmp(command, "save")==0)
            save();
        else if(strcmp(command, "exit")==0)
            break;
    }
}

void add(){
    char buffer1[BUFFER_SIZE], buffer2[BUFFER_SIZE];
    scanf("%s", buffer1);
    scanf("%s", buffer2);

    int i = n-1;

    while(i>=0 && strcmp(names[i], buffer1)>0){
        names[i+1] = names[i];
        numbers[i+1] = numbers[i];
        i--;
    }

    names[i+1] = strdup(buffer1);
    numbers[i+1] = strdup(buffer2);

    n++;

    printf("%s was added successfully. \n",buffer1);
}

void find(){
    char buffer[BUFFER_SIZE];
    scanf("%s", buffer);
    int i;
    for(i=0; i<n; i++){
        if(strcmp(buffer,names[i])==0){
            printf("%s\n", numbers[i]);
        }
    }
    printf("No person named '%s' exists.\n", buffer);
}

void status(){
    int i;
    for(i=0; i<n; i++)
        printf("%s %s\n", names[i], numbers[i]);
    printf("Total %d people.\n", n);
}

int search(char * name){
    int i;
    for(i=0; i<n; i++){
        if(strcmp(name, names[i])==0)
            return i;
    }
    return -1;
}

void delete(){
    char buffer[BUFFER_SIZE];
    scanf("%s", buffer);
    int index = search(buffer);
    if(index==-1)
        printf("No person named %s exists. \n", buffer);

    int i = index;

    for(; i<n-1; i++){
        names[i] = names[i+1];
        numbers[i] = numbers[i+1];
    }
    n--;
    printf("person named %s deleted successfully.\n", buffer);
}

void load(){
    char fileName[BUFFER_SIZE];
    char buffer1[BUFFER_SIZE];
    char buffer2[BUFFER_SIZE];

    scanf("%s", fileName);

    FILE * fp = fopen(fileName,"r");

    if(fp==NULL)
        printf("Open failed.\n");

    while(fscanf(fp,"%s",buffer1) != EOF){
        fscanf(fp, "%s", buffer2);
        names[n] = strdup(buffer1);
        numbers[n] = strdup(buffer2);
        n++;
    }

    fclose(fp);
}

void save(){
    int i;
    char fileName[BUFFER_SIZE];
    char tmp[BUFFER_SIZE];

    scanf("%s", tmp);
    scanf("%s", fileName);

    FILE * fp = fopen(fileName, "w");

    if(fp==NULL)
        printf("Open Failed.\n");
    for(i=0; i<n; i++)
        fprintf(fp, "%s %s\n", names[i], numbers[i]);

    fclose(fp);
}
```

위의 코드의 문제점은 한 단어씩 입력해야 한다는 것입니다.  
add kms 01099150000 이런 식으로 입력을 자연스럽게 받지 못합니다.  
add  
kms 01099150000 이렇게 두번에 걸쳐서 입력을 받아야 하지요.  
문자열 토크나이징이라는 것은 구분문자(delimiter)를 이용해서 긴 문자열을 작은 문자열로 자르는 일을 의미합니다.  
그리고 이렇게 잘라진 문자열을 토큰(token)이라고 합니다.  
C언어에서는 주로 strtok 함수를 이용합니다.  
![strtok](https://impressprogram.github.io/assets/media/strtok.png)  
strtok의 첫 번째 인자로는 문자열의 시작 주소를, 두번째 인자로는 구분자의 시작 주소를 줍니다.  
주의할 점은 strtok는 원본 문자열을 변형시키므로 포인터로 가리키는 문자열은 인자로 받을 수 없습니다.  

```
str[] = "ajfiaoejfow" -> 가능
char * str = "ajifowjoeifwe" -> 불가능
```

처음만 저렇게 주고 두 번째부터는 첫 번째 인자로 NULL값을 줍니다(토큰으로 나눌 때 널값이 삽입되므로).  
아래는 사용예시입니다.

```
#include <stdio.h>
#include <string.h>

int main(void){
    char str[] = "my # name # is # myeongsoo kim # ~!";
    char delim[] = "#";
    char *token;

    token = strtok(str, delim);
    while(token != NULL){
        printf("next token is: %s:%d\n", token, strlen(token));
        token = strtok(NULL, delim);
    }
    return 0;
}
```

이렇게 토크나이징을 이용해서 위의 코드를 개선해봤습니다.

```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define INIT_CAPACITY 3
#define BUFFER_SIZE 50

char ** names; // char * 타입의 배열의 이름이므로 이중포인터로 대체하였습니다
char ** numbers;

int capacity = INIT_CAPACITY;
int n=0;

char delim[] = " ";

void init_directory();
void process_command();

int main(){

    init_directory(); // 이 함수에서 배열 names와 numbers를 생성합니다
    process_command(); // 사용자의 명령을 받아 처리합니다

    return 0;
}

void reallocation(){
    int i;

    capacity *= 2;
    char **tmp1 = (char**)malloc(capacity * sizeof(capacity));
    char **tmp2 = (char**)malloc(capacity * sizeof(capacity));

    for(i=0; i<n; i++){
        tmp1[i] = names[i];
        tmp2[i] = numbers[i];
    }

    free(names);
    free(numbers);

    names = tmp1;
    numbers = tmp2;
}

void init_directory(){
    names = (char **)malloc(INIT_CAPACITY * sizeof(char*));
    numbers = (char**)malloc(INIT_CAPACITY * sizeof(char*));
}

int search(char * name){
    int i;
    for(i=0; i<n; i++){
        if(strcmp(name, names[i])==0)
            return i;
    }
    return -1;
}

int read_line(char str[], int limit){
    int ch, i=0;

    while((ch=getchar()) != '\n'){
        if(i < limit-1)
            str[i++] = ch;
    }
    str[i] = '\0';

    return i;
}

void add(char * name, char * number){
    if(n>=capacity)
        reallocation();
    int i=n-1;
    while(i>=0 && strcmp(names[i], name) > 0){
        names[i+1] = names[i];
        numbers[i+1] = numbers[i];
        i--;
    }

    names[i+1] = strdup(name);
    numbers[i+1] = strdup(number);
    n++;
}

void load(char *fileName){
    char buffer1[BUFFER_SIZE];
    char buffer2[BUFFER_SIZE];

    FILE * fp = fopen(fileName,"r");

    if(fp==NULL)
        printf("Open failed.\n");

    while(fscanf(fp,"%s",buffer1) != EOF){
        fscanf(fp, "%s", buffer2);
        add(buffer1, buffer2);
    }
    fclose(fp);
}

void status(){
    int i;
    for(i=0; i<n; i++)
        printf("%s %s\n", names[i], numbers[i]);
    printf("Total %d people.\n", n);
}

void delete(char * name){
    int i = search(name);

    if(i == -1)
        printf("No person named %s exists.\n", name);

    int j = i;
    for(; j<n-1; j++){
        names[j+1] = names[j];
        numbers[j+1] = numbers[j+1];
    }
    n--;
    printf("'%s' was deleted successfully.\n", name);
}

void save(char *fileName){
    int i;
    FILE * fp = fopen(fileName, "w");

    if(fp==NULL)
        printf("Open failed.\n");

    for(i=0; i<n; i++)
        fprintf(fp, "%s %s \n", names[i], numbers[i]);
    fclose(fp);
}

void find(char * name){
    int index = search(name);
    if(index == -1)
        printf("No name %s exists.\n", name);
    else
        printf("%s \n", numbers[index]);
}


void process_command(){
    char command_line[BUFFER_SIZE];
    char *command, *arg1, *arg2;

    while(1){
        printf("$ ");

        if(read_line(command_line, BUFFER_SIZE)<=0)
            continue;

        command = strtok(command_line, delim);
        if(command == NULL)
            continue;
        if(strcmp(command, "read")==0){
            arg1 = strtok(NULL, delim);
            if(arg1 == NULL){
                printf("File name required.\n");
                continue;
            }
            load(arg1);
        }
        else if(strcmp(command, "add") == 0){
            arg1 = strtok(NULL, delim);
            arg2 = strtok(NULL, delim);
            if(arg1 == NULL || arg2 == NULL){
                printf("Invalid arguments.\n");
                continue;
            }
            add(arg1, arg2);

            printf("%s was added successfully.\n", arg1);
        }

        else if(strcmp(command, "find") == 0){
            arg1 = strtok(NULL, delim);
            if(arg1 == NULL){
                printf("Invalid arguments.\n");
                continue;
            }
            find(arg1);
        }

        else if(strcmp(command, "status") == 0){
            status();
        }

        else if(strcmp(command, "delete") == 0){
            arg1 = strtok(NULL, delim);
            if(arg1 == NULL){
                printf("Invalid arguments.\n");
                continue;
            }
            delete(arg1);
        }

        else if(strcmp(command, "save") == 0){
            arg1 = strtok(NULL, delim);
            arg2 = strtok(NULL, delim);
            if(arg1 == NULL || strcmp(arg1, "as") != 0 || arg2 == NULL){
                printf("Invalid arguments.\n");
                continue;
            }
            save(arg2);

            printf("%s was saved successfully.\n", arg2);
        }

        else if(strcmp(command, "exit")==0)
            break;
    }
}

```