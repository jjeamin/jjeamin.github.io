---
layout: post
title:  "Serialization"
summary: "Serialization JSON, Protobuf"
date:   2020-06-09 09:10 -0400
categories: engineer
---

# Serialization

- 데이터 구조를 바이트 스트림으로 인코딩하는 것

- 데이터 구조를 여러 환경에서 공유하기 위해서 형태를 변환하는 방법

- 네트워크 전송도 가능


```

데이터 구조 == (직렬화) ==> Byte == (역직렬화) ==> 데이터 구조

```

# Protobuf

- `.proto`

- 구글이 만들었다.

- 데이터를 교환하는 Format

- 공식 문서 예제 : [https://developers.google.com/protocol-buffers/docs/pythontutorial](https://developers.google.com/protocol-buffers/docs/pythontutorial)


## Window 설치

- [https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)

- protoc-version-win64.zip 설치

- 압축해제

- 환경변수 등록

- 설치 확인

```
protoc --version
```

## Tutorial

#### `addressbook.proto` 작성

```proto2
syntax = "proto2";

package tutorial;

message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phones = 4;
}

message AddressBook {
  repeated Person people = 1;
}
```

\+ required : 값을 무조건 제공한다.

\+ optional : 값은 선택적 제공한다.

\+ repeated : 값이 여러번 반복된다.

#### Complie

```shell
protoc -I=. --python_out=. ./addressbook.proto
```

- 자세한 입력 형식은 [공식 문서](https://developers.google.com/protocol-buffers/docs)를 읽어보시면 됩니다.

#### writing.py

```python
import addressbook_pb2
import sys

# This function fills in a Person message based on user input.
def PromptForAddress(person):
  person.id = int(input("Enter person ID number: "))
  person.name = input("Enter name: ")

  email = input("Enter email address (blank for none): ")
  if email != "":
    person.email = email

  while True:
    number = input("Enter a phone number (or leave blank to finish): ")
    if number == "":
      break

    phone_number = person.phones.add()
    phone_number.number = number

    type = input("Is this a mobile, home, or work phone? ")
    if type == "mobile":
      phone_number.type = addressbook_pb2.Person.PhoneType.MOBILE
    elif type == "home":
      phone_number.type = addressbook_pb2.Person.PhoneType.HOME
    elif type == "work":
      phone_number.type = addressbook_pb2.Person.PhoneType.WORK
    else:
      print("Unknown phone type; leaving as default value.")

# Main procedure:  Reads the entire address book from a file,
#   adds one person based on user input, then writes it back out to the same
#   file.
if len(sys.argv) != 2:
  print("Usage:", sys.argv[0], "ADDRESS_BOOK_FILE")
  sys.exit(-1)

address_book = addressbook_pb2.AddressBook()

# Read the existing address book.
try:
  f = open(sys.argv[1], "rb")
  address_book.ParseFromString(f.read())
  f.close()
except IOError:
  print(sys.argv[1] + ": Could not open file.  Creating a new one.")

# Add an address.
PromptForAddress(address_book.people.add())

# Write the new address book back to disk.
f = open(sys.argv[1], "wb")
f.write(address_book.SerializeToString())
f.close()
```

#### 실행

```shell
python writing.py person.data
```

#### reading.py

```python
import addressbook_pb2
import sys

# Iterates though all people in the AddressBook and prints info about them.
def ListPeople(address_book):
  for person in address_book.people:
    print("Person ID:", person.id)
    print("  Name:", person.name)
    if person.HasField('email'):
      print("  E-mail address:", person.email)

    for phone_number in person.phones:
      if phone_number.type == addressbook_pb2.Person.PhoneType.MOBILE:
        print("  Mobile phone #: "),
      elif phone_number.type == addressbook_pb2.Person.PhoneType.HOME:
        print("  Home phone #: "),
      elif phone_number.type == addressbook_pb2.Person.PhoneType.WORK:
        print("  Work phone #: "),
      print(phone_number.number)

# Main procedure:  Reads the entire address book from a file and prints all
#   the information inside.
if len(sys.argv) != 2:
  print("Usage:", sys.argv[0], "ADDRESS_BOOK_FILE")
  sys.exit(-1)

address_book = addressbook_pb2.AddressBook()

# Read the existing address book.
f = open(sys.argv[1], "rb")
address_book.ParseFromString(f.read())
f.close()

ListPeople(address_book)
```

#### 읽기

```shell
python reading.py person.data
```

---

## JSON

- JavaScript Object Notation

- [키, 속성 / 값] 쌍으로 이루어진 데이터

- Object는 중괄호({})로 감싼다.

- Array는 대괄호([])로 감싼다.

- 쉼표(,)로 나열된다.


#### Object

```json
{
  "name": "JJM",
  "age" : 26,
}
```

#### Array

```json
"array": [
  {
    "name": "LL",
    "age": 1
  },
  {
    "name": "KK",
    "age": 2
  },
  {
    "name": "HH",
    "age": 3
  }
]
```

#### Serialization

JSON -> String

```JavaScript
var person = {
  "name": "JJM",
  "age" : 26,
}

var jsonText = JSON.stringify(person);
```

출력 : `"{"name":"JJM","age":26}"`

#### DeSerialization

String -> JSON

```JavaScript
JSON.parse(jsonText)
```

출력 : `{name: "JJM", age: 26}`
