---
layout: post
comments: true
title: 포인터를 이용한 복사 비용 감소시키기
categories: [datastructure]
tags: [c, datastructure]
fullview: true
---

![포인터의장점1](https://impressprogram.github.io/assets/media/ref-pointer1.png)  

위의 그림은 이전 코드의 단점을 나타내어 준 것입니다.  
함수간에서 파라메타로 값을 받을 때 call-by-reference이므로 일일히 값의 복사가 일어나게 되고 이것은 프로그래밍 성능의 저하를 일으킵니다.  

![포인터의장점2](https://impressprogram.github.io/assets/media/ref-pointer2.png)  

그리고 위와 같이 return값을 줄 때에도 임시 객체가 새로 생성되기 때문에 값의 복제가 일어나는 것과 같습니다.  
엄청난 프로그래밍적 손실이라고 볼 수 있습니다.  

![포인터의장점3](https://impressprogram.github.io/assets/media/ref-pointer3.png)  

위와 같이 값을 추가하거나 제거할 때에도 데이터가 그 값을 전후로 전부 이동해야 하기 때문에 상당히 비효율적인 코드가 됩니다.  
포인터를 이용하면 위와 같은 3가지 현상에 대한 성능저하를 개선시킬 수 있습니다.  
포인터를 이용하면 각 데이터를 일일히 옮기지 않아도 주소값을 한 번만 옮겨주면 되기 때문이지요.  
이를 이용해서 개선시킨 코드는 아래와 같습니다.  

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define INIT_CAPACITY 100
#define BUFFER_LENGTH 100

typedef struct _Person {
    char *name;
    char *number;
    char *email;
    char *group;
}Person;

// Person타입의 배열
Person ** directory;
// 디렉토리에 있는 사람 명수
int capacity;
int n=0;

//void init_directory();
void process_command();
void init();

int main(){

    init();
    process_command(); // 사용자의 명령을 받아 처리합니다

    return 0;
}

void reallocate(){
    capacity *=2;
    Person **tmp = (Person**)malloc(sizeof(Person*));
    for(int i=0; i<n; i++)
        tmp[i] = directory[i];
    free(directory);
    directory=tmp;
}

void init(){
    directory = (Person**)malloc(INIT_CAPACITY*sizeof(Person *));
    capacity = INIT_CAPACITY;
    n = 0;
}

int search(char * name){
    int i;
    for(i=0; i<n; i++){
        if(strcmp(name, directory[i]->name)==0)
            return i;
    }
    return -1;
}

int read_line(FILE * fp, char str[], int limit){
    int ch, i=0;

    while((ch=fgetc(fp)) != '\n' && ch != EOF){
        if(i < limit-1)
            str[i++] = ch;
    }
    str[i] = '\0';

    return i;
}

void add(char * name, char * number, char * email, char * group){
    if(n>=capacity)
        reallocate();
    int i = n-1;
    while(i>=0 && strcmp(directory[i]->name, name)){
        directory[i+1] = directory[i];
        i--;
    }
    directory[i+1] = (Person *)malloc(sizeof(Person)); // 이제는 값을 복사하고 빈 칸에 채워 넣는 것이 아니고 주소값들을 옮겨주고 새 주소값을 할당받아서 껴넣어야합니다.
    directory[i+1]->name = name;
    directory[i+1]->number = number;
    directory[i+1]->email = email;
    directory[i+1]->group = group;

    n++;
}

void print_person(Person *p){
    printf("%s :\n", p->name);
    printf("number : %s\n", p->number);
    printf("email : %s\n", p->email);
    printf("group : %s\n", p->group);

}

void load(char *fileName){
    char buffer[BUFFER_LENGTH];
    char *name, *number, *email, *group;
    char * token;

    FILE * fp = fopen(fileName,"r");

    if(fp==NULL)
        printf("Open faled.\n");

    while(1){
        if(read_line(fp, buffer, BUFFER_LENGTH)<=0)
            break;
        name = strtok(buffer, "#");
        token = strtok(NULL,"#");
        if(strcmp(token, " ")==0)
            number = NULL;
        else
            number = strdup(token);
        token = strtok(NULL, "#");
        if(strcmp(token, " ")==0)
            email = NULL;
        else
            email = strdup(token);
        token = strtok(NULL, "#");
        if(strcmp(token, " ")==0)
            group = NULL;
        else
            group = strdup(token);

        add(strdup(name), number, email, group);
    }
    fclose(fp);
}

void status(){
    int i;
    for(i=0; i<n; i++)
        print_person(directory[i]);
    printf("Total %d people.\n", n);
}

void release_person(Person *p){
    free(p->name);
    if(p->number!=NULL) free(p->number);
    if(p->email!=NULL) free(p->email);
    if(p->group!=NULL) free(p->group);
    free(p);
}

void delete(char * name){
    int i = search(name);
    if(i == -1)
        printf("No person named %s exists.\n", name);

    Person *p = directory[i];

    for(int j=1; j<n-1; j++){
        directory[j] = directory[j+1];
    }
    n--;
    release_person(p);
    printf("'%s' was deleted successfully.\n", name);
}

void save(char *fileName){
    int i;
    FILE * fp = fopen(fileName, "w");

    if(fp==NULL)
        printf("Open failed.\n");

    for(i=0; i<n; i++){
        fprintf(fp, "%s#", directory[i]->name);
        fprintf(fp, "%s#", directory[i]->number);
        fprintf(fp, "%s#", directory[i]->email);
        fprintf(fp, "%s#\n", directory[i]->group);
    }
    fclose(fp);
}

void find(char * name){
    int index = search(name);
    if(index == -1)
        printf("No name %s exists.\n", name);
    else
        print_person(directory[index]);
}

int compose_name(char str[], int limit){
    char *ptr;
    int length = 0;

    ptr = strtok(NULL, " ");
    if(ptr==NULL)
        return 0;

    strcpy(str, ptr);
    length += strlen(ptr);

    while((ptr = strtok(NULL, " ")) != NULL){
        if(length+strlen(ptr)+1<limit){
            str[length++] = ' ';
            str[length] = '\0';
            strcat(str, ptr);
            length += strlen(ptr);
        }
    }
    return length;
}

void handle_add(char * name){
    char number[BUFFER_LENGTH], email[BUFFER_LENGTH], group[BUFFER_LENGTH];

    printf("Phone : ");
    read_line(stdin, number, BUFFER_LENGTH);

    printf("Email : ");
    read_line(stdin, email, BUFFER_LENGTH);

    printf("Group : ");
    read_line(stdin, group, BUFFER_LENGTH);

    add(name, (char*)strlen(number)>0 ? strdup(number) : NULL, (char*)strlen(email)>0 ? strdup(email) : NULL, (char*)strlen(group)>0 ? strdup(group) : NULL);
}


void process_command(){
    char command_line[BUFFER_LENGTH];
    char *command, *argument;
    char name_str[BUFFER_LENGTH];

    while(1){
        printf("$ ");

        if(read_line(stdin, command_line, BUFFER_LENGTH)<=0)
            continue;

        command = strtok(command_line, " ");
        if(command == NULL)
            continue;
        if(strcmp(command, "read")==0){
            argument = strtok(NULL, " ");
            if(argument == NULL){
                printf("File name required.\n");
                continue;
            }
            load(argument);
        }
        else if(strcmp(command, "add") == 0){
            if(compose_name(name_str,BUFFER_LENGTH) <= 0){ // 불필요한 공백들을 제거해줍니다.
                printf("Name required.\n");
                continue;
            }

            handle_add(name_str);
        }

        else if(strcmp(command, "find") == 0){
            if(compose_name(name_str,BUFFER_LENGTH) <= 0){
                printf("Name required.\n");
                continue;
            }

            find(name_str);
        }

        else if(strcmp(command, "status") == 0){
            status();
        }

        else if(strcmp(command, "delete") == 0){
            if(compose_name(name_str,BUFFER_LENGTH) <= 0){ // 불필요한 공백들을 제거해줍니다.
                printf("Name required.\n");
                continue;
            }

            delete(name_str);
        }

        else if(strcmp(command, "save") == 0){
            argument = strtok(NULL, " ");
            if(strcmp(argument, "as")!=0){
                printf("Invalid arguments.\n");
                continue;
            }
            argument = strtok(NULL, " ");
            if(argument == NULL){
                printf("Invalid arguments.\n");
                continue;
            }
            save(argument);

            printf("%s was saved successfully.\n", argument);
        }

        else if(strcmp(command, "exit")==0)
            break;
    }
}
```
