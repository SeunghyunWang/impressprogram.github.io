---
layout: post
comments: true
title: C++ 파일 입출력
categories: [cpp]
tags: [cpp, programming-language]
fullview: true
---

# 파일 입출력의 기본

- 파일을 쓸 때에는 ofstram을 이용합니다.
- 파일을 읽을 때에는 ifstream을 이용합니다.
- ofstream, ifstream 객체는 iostream 객체로부터 파생되었습니다.
- #include <fstream>를 포함해야 이 둘을 사용할 수 있습니다.

ifstream 사용예시

```
#include <iostream>
#include <string>
#include <fstream>
using namespace std;

int main()
{
	// 파일 읽기 준비
	ifstream in("test.txt");

	string s;
	if (in.is_open()) {
		while (!in.eof()) {
			getline(in, s);
			cout << s << endl;
		}
	}
	else {
		cout << "파일을 찾을 수 없습니다!" << endl;
	}

	return 0;
}
```

# 조건을 나타내는 상태

- iostream 객체는 입력과 출력 상태를 알리는 플래그를 가지고 있다.
- 불린 함수 eof(), bad(), fail(), good()를 사용하여 이 플래그들을 검사할 수 있다.
- eof() : iostream 객체가 EOF를 만나면 참값을 반환한다.
- bad() :  유효하지 않은 연산자를 시도하려면 참값을 반환
- fail() : bad() 함수가 실패하면
- good() : 위의 세 개의 함수가 거짓을 참을 반환한다.

# 입출력을 위한 파일 열기

- 만약 ttext.txt 파일을 ofstream 객체로 열기 위해 ofstream 객체의 인스턴스를 선언하고 매개 변수로 파일 이름을 전달한다.
- ofstream fout(“text.txt”);
- 입력용으로 열기 위한 방법
- ifstream fin(“text.txt”);
- close()
- 읽기용이나 쓰기용, 읽고 동시에 쓰기용으로 열어놓은 파일은 입출력을 마친 뒤 사용을 한다.

# 순차 접근 파일 생성

```
#include<iostream>

#include<fstream>
using std::ofstream; //파일에 쓰기용


File Access Type

ios::app 파일 끝에 모든 출력을 추가
ios::ate 출력을 위해 파일을 열고 파일의 끝으로 이동
ios::in 입력용 파일 열기
ios::out 출력용 파일 열기
ios::trunc 파일이 존재한다면, 파일 내용을 모두 삭제(ios::out을 위한 디폴트 동작)
ios::binary 텍스트가 아닌 이진으로 입출력하기 위한 파일열기


int main()
{

   int accoount;
   char name[30];
   double balance;

  ofstream outFile("test.dat",ios::out); //파일을 연다, 없으면 생성
  while(cin>>account>>name>>balance)
      outFile<<account<<' '<<name<<' '<<balance<<endl;  //이런형식으로 저장됨
  return 0;

}
```

# 순차 접근 파일에서데이터 읽기

```
using std::ifstream; //파일 읽기용

int main()
{

   int accoount;
   char name[30];
   double balance;

  ifstream inFile("test.dat",ios::in);   //읽을 파일을 연다.
  while(inFile>>account>>name>>balance)
      cout << account << ' ' << name << ' ' << balance << endl;  //이런형식으로 저장됨
  return 0;

}
```

istream : seekg(seek get) //읽기용 포인터 탐색  
ostream : seekp(seek put) //쓰기용 포인터 탐색  

예)  
  fileObject.seekg(n); //fileObject의 n바이트로 이동(디폴트ios::beg)  
  fileObject.seekg(n, ios::cur); //fileObject의 현재 위치에서 n바이트 앞으로 이동  
  fileObject.seekg(n, ios::end); //fileObject의 끝에서 뒤로 n바이트로 이동  
  fileObject.seek(0, ios::end); //fileObject의 끝으로 이동  

```
#include<fstream>

using std::ofstream;
using std::ifstream;

typedef struct {
   char name[16];
   int    id[20];
   int age;
}  Student;

Student memb;

int main()
{

// case 1
   ifstream inFile("testIn.dat",ios::in);
  inFile.read(reinterpret_cast<char*>(&memb), sizeof(Student));

  ofstream outFile("testOut.data",ios::binary);
  outFile.seekp(0, ios::end);
  outFile.write(reinterpret_cast<const char*>(&memb),sizeof(Student));

// Case 2
  while(inFile&& !inFile.eof())  {    //파일의 끝까지 모든 레코드를 읽음
      inFile.read(reinterpret_cast<char*>(&memb),sizeof(Student));       //다음 레코드를 읽음
      outFile.write(reinterpret_cast<const char*>(&memb),sizeof(Student));
  }

// Case 3
  fstream outFile("testmemb.dat",ios::in | ios::out); //읽고 쓰기위한 파일 열기

  return 0;
}
```

# 파일 열고 쓰기 예

```
/*
*  읽기와쓰기용으로파일을열기
*/

#include <fstream>
#include <iostream>
using namespace std;

char gbuffer[1024];    
char fileName[80];

int main()
{
        cout << "file name: ";
        cin >> fileName;

        /// 쓰기용
        ofstream fout(fileName);
        fout << "this line written directory to the file.....\n";
        cout << "Enter text for the file:";
        /// 파일뒤에개행문자를없앤다.
        cin.ignore(1,'\n');

        /// 사용자입력을받는다.
        cin.getline(gbuffer, 255);

        /// 파일에쓴다.
        fout << gbuffer<< "\n";

        /// 파일객체를닫고다시열기를기다린다.
        fout.close();

        /// 읽기를위해서스트림을다시연다
        ifstream fin(fileName);
        cout << "Here's the contents of the file: \n";
        char ch;
        while (fin.get(ch))
        {
               cout << ch;
        }

        cout << "\n";
        cout << "### End of file contents ###\n";
        fin.close();
        return 0;
}
```

출처 : http://blog.naver.com/dolicom/10086114631
